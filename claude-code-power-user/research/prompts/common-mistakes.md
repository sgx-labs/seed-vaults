---
title: "Token-Wasting Mistakes to Avoid in Claude Code"
tags: [prompts, mistakes, efficiency, tokens, antipatterns]
content_type: research
domain: engineering
---

# Token-Wasting Mistakes to Avoid in Claude Code

## The Core Idea

Most token waste does not come from hard tasks. It comes from habits carried over from chatbot interactions, lack of awareness about how the agent works, and failure to use the tools Claude Code provides. Here are the most common mistakes and what to do instead.

## 1. Rapid-Fire Single-Line Prompts

### The Mistake

```
You: "Open src/api/users.ts"
You: "Find the createUser function"
You: "Add email validation to it"
You: "Now add a test"
You: "Put the test in the tests directory"
```

Five prompts. Each one reloads the system context (~3K tokens). Total overhead: ~15K tokens. Claude also loses the thread between messages and may re-read files it already saw.

### The Fix

```
You: "Add email validation to the createUser function in src/api/users.ts.
      Reject malformed emails with a 400 response.
      Add a test in src/api/__tests__/users.test.ts covering valid email,
      invalid email, and missing email."
```

One prompt, same result, a third of the cost.

### The Rule

If your next prompt is a continuation of your current thought, stop and combine them into one message.

## 2. Pasting Code That Claude Can Read

### The Mistake

```
You: "Here's my current function:
      function processOrder(order) { ... [80 lines] ... }
      Can you add tax calculation to this?"
```

~500 tokens pasting code Claude can read from disk for free.

### The Fix

```
You: "Add tax calculation to processOrder() in src/services/orders.ts.
      Apply the tax rate from config.TAX_RATE after the subtotal."
```

Claude reads the file itself and sees the full, up-to-date version. Only paste code that does not exist in the filesystem: terminal output, external snippets, or documentation examples.

## 3. Repeating Context That Is Already in CLAUDE.md

### The Mistake

```
You: "Remember, we use TypeScript with strict mode, our test framework
      is Vitest, we use Prisma for the database, all API responses should
      follow the standard format in src/types/api.ts, and error codes are
      defined in src/constants/errors.ts."
```

If this is in your CLAUDE.md, Claude already read it at session start. You are spending ~200 tokens to tell Claude what it already knows.

### The Fix

Put persistent project context in CLAUDE.md once. Reference it by section if you need to emphasize something:

```
You: "Add the new endpoint. Make sure to follow the API response
      format — I've seen inconsistencies creeping in."
```

Claude re-reads the relevant section when reminded. No need to restate it.

### When to Repeat

Repeat context only when you are overriding a default. If CLAUDE.md says "use Vitest" but this one file needs Jest for compatibility, say so explicitly.

## 4. Asking "Can You" Instead of Just Asking

### The Mistake

```
You: "Can you look at the auth middleware and see if there might be
      any issues with how it handles expired tokens?"
```

Polite, but 40 tokens of hedging that produces analysis instead of a fix.

### The Fix

```
You: "Review src/middleware/auth.ts for expired token bugs. Fix any you find."
```

Direct instructions produce direct results. More examples:

```
Hedging:  "Would it be possible to add input validation?"
Direct:   "Add input validation to the CreateOrder handler."

Hedging:  "Do you think there might be a bug in...?"
Direct:   "Check src/utils/date.ts for off-by-one errors."
```

## 5. Not Providing Error Messages for Bug Reports

```
Bad:  "The API is returning errors when I create a user."
      → Claude guesses, searches, explores. 10-20K tokens of exploration.

Good: "Error: TypeError: Cannot read property 'toLowerCase' of undefined
       at validateEmail (src/utils/validation.ts:23)
       Input: { name: 'Test', email: null }
       Expected: 400 validation error. Actual: 500."
      → Claude reads one file, fixes it in one pass.
```

## 6. Fighting the Agent's Approach

### The Mistake

```
Claude: [proposes EventEmitter pattern]
You: "No, use callbacks."  →  "Actually, promises."  →  "What about generators?"
```

Four iterations, three thrown away. Each rewrite costs thousands of tokens.

### The Fix

If you are unsure about the approach, use plan mode first:

```
You: "I need real-time dashboard updates. Use plan mode.
      Compare: WebSocket, Server-Sent Events, polling.
      Recommend one based on our existing stack."
```

Review the plan. Pick an approach. Implement once. Course-correcting once is fine. Serial indecision is not.

## 7. Over-Explaining What the Code Does

```
Bad:  "In calculateDiscount, there's a loop over cart items that checks
       discount codes, looks up percentages, applies them..."  [150 tokens]

Good: "Fix the bug in calculateDiscount() in src/cart/pricing.ts where
       percentage discounts over 50% are applied twice."
```

Claude reads code faster than you can describe it. State the problem, not a walkthrough. Only explain behavior that is not obvious from reading the source: business rules, external system interactions, or timing-dependent conditions.

## 8. Not Using Plan Mode for Complex Tasks

### The Mistake

```
You: "Refactor auth to use OAuth2. Update the schema, middleware,
      routes, pages, and tests."
```

Claude dives in. You get 20 file changes that may not be what you wanted.

### The Fix

```
You: "Refactor auth to use OAuth2. Use plan mode.
      Show me the plan with file-by-file changes before implementing."
```

Plan mode costs ~2-3K tokens. A wrong implementation that gets reverted costs 20-50K. Planning is the cheapest operation in Claude Code.

## Checklist: Before You Send a Prompt

Run through this list before hitting enter:

- [ ] Did I combine multiple related requests into one prompt?
- [ ] Did I reference files by path instead of pasting code?
- [ ] Am I repeating context that is already in CLAUDE.md?
- [ ] Did I state what I want directly, without hedging?
- [ ] Did I include error messages and file locations for bugs?
- [ ] Do I know which approach I want, or should I use plan mode?
- [ ] Am I describing code Claude can read, or context it cannot?

Getting even half of these right will noticeably reduce your daily token spend.
