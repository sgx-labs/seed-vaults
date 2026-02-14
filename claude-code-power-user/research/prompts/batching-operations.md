---
title: "How to Batch Operations for Efficiency in Claude Code"
tags: [prompts, batching, efficiency, tokens, cost]
content_type: research
domain: engineering
---

# How to Batch Operations for Efficiency in Claude Code

## The Core Idea

Every prompt you send to Claude Code carries overhead: system prompt reload, CLAUDE.md injection, tool initialization, and context reconstruction. This overhead runs 2-5K tokens per turn regardless of whether you ask Claude to rename a variable or redesign an entire module. Batching related changes into a single prompt amortizes that cost.

## The Cost of Rapid-Fire Prompts

Here is what happens when you send five separate prompts to update five API endpoints:

```
Prompt 1: "Update src/api/users.ts to use the new error format"
  → System reload: 3K tokens
  → Read file: 800 tokens
  → Edit: 400 tokens
  → Total: ~4.2K tokens

Prompt 2: "Update src/api/orders.ts to use the new error format"
  → System reload: 3K tokens  ← same overhead again
  → Read file: 600 tokens
  → Edit: 350 tokens
  → Total: ~3.95K tokens

...repeat 3 more times
```

**Total for 5 separate prompts: ~20K tokens, 15K of which is overhead.**

Now the batched version:

```
Prompt: "Update all endpoint handlers in src/api/ to use the new
         error format from src/utils/errors.ts. Apply to:
         users.ts, orders.ts, products.ts, auth.ts, and settings.ts."
  → System reload: 3K tokens   ← once
  → Read 5 files: 3K tokens
  → Edit 5 files: 2K tokens
  → Total: ~8K tokens
```

**Same result, 60% fewer tokens.**

## What Makes a Good Batch

Good batches share these properties:

1. **Same type of change** — all following the same pattern
2. **Related files** — in the same module or feature area
3. **Independent edits** — the change to file A does not depend on seeing the result in file B first
4. **Clear specification** — you can describe the pattern once and it applies to all

### Example: Updating Error Handling Across a Module

```
Update all handlers in src/api/ to:
1. Wrap database calls in try/catch
2. Return { error: string, code: number } on failure
3. Log errors using the logger from src/utils/logger.ts
4. Return 500 for unexpected errors, 400 for validation, 404 for not found

Apply to: users.ts, orders.ts, products.ts, payments.ts
```

### Example: Adding TypeScript Types

```
Add proper TypeScript types to the five service files in src/services/.
Replace all `any` types with specific interfaces. Put shared interfaces
in src/types/services.ts. Each service file should import from there.
```

### Example: Test Coverage

```
Write unit tests for the three utility functions in src/utils/validation.ts:
- validateEmail: valid format, invalid format, empty string, null
- validatePhone: US format, international, invalid, empty
- validateAge: valid range, negative, zero, non-number
Put tests in src/utils/__tests__/validation.test.ts
```

## When NOT to Batch

### 1. Unrelated Tasks

```
Bad batch: "Add authentication to the API, fix the CSS on the
            login page, and update the README."
```

These are three different concerns. Claude will context-switch between them, produce worse results for each, and if one fails, you have to untangle which parts succeeded.

```
Better: Three separate, focused prompts — one per concern.
```

### 2. Tasks That Need Intermediate Review

```
Bad batch: "Redesign the database schema, then update all the
            queries, then update all the API handlers."
```

You want to review the schema before Claude rewrites 30 query files based on it. If the schema is wrong, all that query work is wasted.

```
Better:
  Prompt 1: "Design a new schema for the orders system. Use plan mode."
  [Review the plan. Approve or adjust.]
  Prompt 2: "Implement the schema changes and update queries in src/db/"
  [Verify queries work.]
  Prompt 3: "Update API handlers in src/api/ to use the new query functions."
```

### 3. Exploratory Work

```
Bad batch: "Find all the performance bottlenecks and fix them."
```

You do not know what the bottlenecks are yet. Claude needs to investigate first, then you decide what to fix.

```
Better:
  Prompt 1: "Profile the API response times. Which endpoints are slowest?"
  [Review findings.]
  Prompt 2: "Optimize the three slowest endpoints you identified."
```

### 4. Complex Architecture Decisions

```
Bad batch: "Refactor the monolith into microservices."
```

This requires design decisions at every step. Batch it and you get Claude's best guess at your architecture — which may not be what you want.

## The Sweet Spot: 1-3 Related Changes

The ideal batch is 1-3 related changes that follow the same pattern:

```
One focused change:
  "Add input validation to the CreateOrder handler in src/api/orders.ts"

Two related changes:
  "Add input validation to CreateOrder and UpdateOrder in src/api/orders.ts.
   Validate that quantity > 0, price > 0, and productId exists."

Three related changes:
  "Add input validation to all three write handlers in src/api/orders.ts
   (Create, Update, Delete). Validate inputs per the OpenAPI spec in docs/api.yaml."
```

Beyond three unrelated files, the prompt gets complex enough that Claude may miss details. Beyond five files with the same change, it usually works fine because the pattern is repetitive.

## Batching Patterns That Work Well

| Pattern | Example |
|---------|---------|
| Same change, many files | "Add logging to all handlers in src/api/" |
| Related feature across layers | "Add soft delete to the User model, repository, and API handler" |
| Test suite for one module | "Write tests for all exported functions in src/utils/dates.ts" |
| Consistent formatting | "Update all API responses in src/api/ to use camelCase keys" |
| Dependency updates | "Update all imports from old-lib to new-lib across the project" |

## Batching Patterns That Do NOT Work

| Pattern | Why It Fails |
|---------|-------------|
| Unrelated bug fixes | Each bug needs investigation; mixing them causes confusion |
| Design + implementation | You need to review the design before committing to implementation |
| Frontend + backend changes | Different concerns, different review needs |
| "Fix everything in this file" | Too vague; Claude does not know your priorities |

## Measuring Whether You Are Batching Well

Use `/cost` at the end of a session. If your average token spend per meaningful change is:

- **Under 5K tokens** — you are batching efficiently
- **5-15K tokens** — normal for moderately complex work
- **Over 20K tokens per change** — you are probably sending too many small prompts or including unnecessary context

The goal is not minimum tokens. The goal is maximum useful output per dollar spent.
