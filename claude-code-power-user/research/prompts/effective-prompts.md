---
title: "How to Write Effective Prompts for Claude Code"
tags: [prompts, efficiency, tokens, fundamentals, four-part-formula, file-paths]
content_type: research
domain: engineering
---

# How to Write Effective Prompts for Claude Code

## The Core Idea

Claude Code is a coding agent, not a chatbot. It can read your files, run commands, and make changes. The best prompts give it a clear target and let it figure out the implementation. The worst prompts either over-explain things Claude can read for itself, or under-specify things Claude cannot guess.

## The 4-Part Formula

Every effective prompt answers four questions:

1. **What** — the goal, stated as an outcome
2. **Where** — file paths or locations in the codebase
3. **Why** — context that affects implementation decisions
4. **Constraints** — things to avoid or preserve

You do not always need all four. Simple tasks need one or two. Complex tasks benefit from all four.

### Example: All Four Parts

```
Add request logging middleware to the Express app.        ← What
It should go in src/middleware/logger.ts.                 ← Where
We're debugging intermittent 502s in production           ← Why
  so log method, URL, status code, and response time.
Don't use any external logging libraries — use            ← Constraints
  console.log with JSON format.
```

### Example: Just What and Where

```
Add input validation to src/api/handlers/users.go for the CreateUser endpoint.
```

Claude will read the file, understand the existing patterns, and implement appropriate validation. No need to explain what validation means.

## Good vs Bad Prompts

### Feature Development

```
Bad:  "Can you help me add authentication to my app?"
Good: "Add JWT auth middleware to src/middleware/auth.ts.
       Verify tokens using the secret in env.AUTH_SECRET.
       Apply it to all routes in src/routes/api/ except /health."
```

The bad prompt forces Claude to ask clarifying questions. The good prompt is immediately actionable.

### Bug Fixing

```
Bad:  "The login page is broken."
Good: "TypeError: Cannot read property 'email' of undefined
       at src/handlers/login.ts:47
       Happens when the user submits the form with an empty password field."
```

Error messages and file locations are the two most valuable things you can give Claude for bug fixes.

### Refactoring

```
Bad:  "Clean up the database code."
Good: "Extract the SQL query builder from src/db/users.ts into
       src/db/query-builder.ts. Keep the existing function signatures
       in users.ts unchanged — they should call the new module."
```

### Code Review

```
Bad:  "Review my code."
Good: "Review src/api/payments.ts for security issues.
       Focus on input validation and SQL injection."
```

## Why File Paths Beat Descriptions

Claude Code reads files by path. When you say "the user service," Claude has to search for it. When you say `src/services/user.ts`, Claude reads it immediately.

Compare the token cost:

```
"Update the function that handles user registration in
 the authentication module"
→ Claude searches: glob, grep, read 3-4 files to find it
→ ~3-5K extra tokens spent on search

"Update registerUser() in src/auth/register.ts"
→ Claude reads the file directly
→ ~200 tokens spent
```

File paths are free to you and expensive for Claude to discover on its own. Always provide them when you know them.

## How Specificity Saves Tokens

Every ambiguity in your prompt costs tokens. Claude resolves ambiguity by reading more files, asking clarifying questions, or making assumptions that may need correction.

```
Vague:    "Add error handling to the API"
          → Claude reads every API file, implements error handling
             everywhere, uses 50K+ tokens

Specific: "Add try/catch with proper HTTP error responses to the
           three handlers in src/api/orders.ts. Return 400 for
           validation errors, 404 for missing orders, 500 for
           unexpected errors."
          → Claude reads one file, makes targeted changes,
             uses ~8K tokens
```

The specific prompt costs less and produces exactly what you want.

## Chatbot Prompting vs Agent Prompting

If you have used ChatGPT or Claude.ai, you are used to chatbot-style prompting: conversational, iterative, exploratory. Agent prompting is different.

### Chatbot Style (Wasteful in Claude Code)

```
You: "I need to add a caching layer."
Claude: "What kind of caching? In-memory, Redis, or..."
You: "In-memory is fine."
Claude: "Where should the cache be used?"
You: "For the API responses in the product endpoints."
Claude: "OK, here's my plan..."
```

Four messages. Each one reloads system context. Each one costs tokens.

### Agent Style (Efficient in Claude Code)

```
You: "Add in-memory caching (TTL: 5 minutes) to the product
      endpoints in src/api/products.ts. Cache by request URL.
      Add a /cache/clear endpoint for manual invalidation."
```

One message. Claude reads the file, implements, and tests. Done.

### The Rule of Thumb

Pretend each prompt costs you $1 in overhead (it roughly does for complex tasks). Would you send five $1 messages or one $1 message that contains all the information? Front-load context. Be direct. State the outcome.

## Quick Reference

| Do | Do Not |
|----|--------|
| Provide file paths | Describe file locations vaguely |
| State the desired outcome | Describe the steps to get there |
| Include error messages verbatim | Paraphrase errors from memory |
| Set constraints upfront | Correct course after implementation |
| Give one detailed prompt | Send five thin prompts |
| Let Claude read the code | Paste code into the chat |
| Reference patterns: "follow the style in X" | Explain coding patterns in prose |

## When Minimal Prompts Work

Not every prompt needs the full formula. For well-structured codebases with clear patterns:

```
"Add a DELETE endpoint to src/api/users.ts following the same pattern as the existing PUT endpoint."
```

Claude reads the file, sees the pattern, and replicates it. The existing code is the specification. The less ambiguity in your codebase, the less you need to write in your prompts.
