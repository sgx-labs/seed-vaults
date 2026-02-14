---
title: "How Prompting Differs by Model in Claude Code"
tags: [prompts, models, opus, sonnet, haiku, cost, performance, model-switching]
content_type: research
domain: engineering
---

# How Prompting Differs by Model in Claude Code

## The Core Idea

Claude Code supports multiple models: Opus, Sonnet, and Haiku. They differ in capability, speed, and cost. Using the right model for the right task is one of the biggest levers you have for balancing quality and spend. Most developers should default to Sonnet, reach for Opus on hard problems, and use Haiku for fast lookups and subagent tasks.

## The Three Models at a Glance

| | Opus | Sonnet | Haiku |
|---|------|--------|-------|
| **Best for** | Architecture, complex refactors, multi-file reasoning | Daily coding, feature work, bug fixes | Quick research, simple edits, subagent tasks |
| **Relative cost** | ~5x Haiku | ~2.5x Haiku | 1x (baseline) |
| **Speed** | Slowest | Medium | Fastest |
| **Context handling** | Best at long, complex context | Good for most tasks | Adequate for focused tasks |
| **When to use** | When Sonnet gets it wrong or the task spans 10+ files | Default for everything | When speed matters more than depth |

## When to Use Opus

Reach for Opus when the task requires:

- **Multi-step reasoning across many files** — understanding how a change in module A affects modules B, C, and D
- **Architecture decisions** — designing a new system or restructuring an existing one
- **Complex refactoring** — moving logic across module boundaries while maintaining correctness
- **Debugging subtle issues** — race conditions, state management bugs, edge cases that require deep reasoning

### Example: Architecture Task for Opus

```
/model opus

I need to split the monolithic request handler in src/server/handler.ts (800 lines)
into separate middleware modules. Use plan mode first.

Requirements:
- Each middleware handles one concern (auth, validation, rate limiting, logging, error handling)
- They compose using the existing middleware chain in src/server/app.ts
- Request context flows through a typed context object, not global state
- All existing tests must continue to pass
```

This is a task where Opus earns its cost. It needs to understand the entire handler, identify logical boundaries, design the middleware interfaces, and ensure nothing breaks during the split.

### Example: Subtle Bug for Opus

```
/model opus

There's a race condition in src/workers/queue.ts.
Occasionally two workers process the same job.
The lock in processJob() (line 45) uses a simple boolean.
Design a proper locking mechanism using the existing Redis connection.
Consider: lock expiry, dead worker cleanup, and retry semantics.
```

## When to Use Sonnet

Sonnet is the daily driver. Use it for:

- **Feature development** — adding endpoints, components, functions
- **Bug fixes** — most bugs with clear error messages and file locations
- **Test writing** — generating test suites from specifications
- **Code review** — reviewing files or PRs for issues
- **Documentation** — writing docs, comments, READMEs
- **Routine refactoring** — renaming, extracting functions, reorganizing imports

### Example: Feature Work for Sonnet

```
Add a /api/v2/search endpoint to src/api/search.ts.
Accept query, page, and limit parameters.
Use the existing SearchService in src/services/search.ts.
Return results in the standard API response format from src/types/api.ts.
```

Sonnet handles this comfortably. One new endpoint, clear pattern to follow, limited file scope.

### Example: Test Writing for Sonnet

```
Write unit tests for src/utils/url-parser.ts.
Cover: valid URLs, relative paths, URLs with query strings,
       malformed input, empty strings, and Unicode paths.
```

## When to Use Haiku

Haiku is the speedster. Use it for:

- **Quick lookups** — "What does the processOrder function in src/api/orders.ts return?"
- **Simple edits** — renaming a variable, fixing a typo, updating a constant
- **Subagent tasks** — when Claude Code spawns subtasks, Haiku keeps costs low
- **Exploratory search** — "Find all files that import the deprecated UserV1 type"
- **Generating boilerplate** — repetitive code that follows an obvious pattern

### Example: Quick Fix for Haiku

```
/model haiku

Change the DEFAULT_TIMEOUT constant in src/config/constants.ts from 5000 to 10000.
```

No reason to pay Opus prices for a one-line change.

### Example: Codebase Research for Haiku

```
/model haiku

List all files that import from the old src/utils/legacy/ directory.
I need to know how many files to update before I remove it.
```

## Switching Models Mid-Session

Use the `/model` command to switch:

```
/model opus      Switch to Opus for a complex task
/model sonnet    Switch back to Sonnet for routine work
/model haiku     Switch to Haiku for a quick lookup
```

A typical session might look like:

```
[Sonnet] "Add the new UserProfile component to src/components/"
[Sonnet] "Write tests for UserProfile"
[Sonnet] "Fix the type error in src/api/users.ts line 42"
[Opus]   "Redesign the state management in src/store/ to use
          the new event-driven pattern. Plan mode first."
[Sonnet] "Implement the plan from above"
[Haiku]  "Find all TODO comments in the codebase"
```

You can also set a default model so you do not have to specify it each time. Sonnet is the recommended default.

## Cost-Aware Prompting

The same prompt costs different amounts on different models. Here is a rough comparison for a typical feature implementation (read 3 files, write 1, run tests):

| Model | Input Tokens | Output Tokens | Approximate Cost |
|-------|-------------|---------------|-----------------|
| Opus | ~15K | ~3K | ~$0.30 |
| Sonnet | ~15K | ~3K | ~$0.12 |
| Haiku | ~15K | ~3K | ~$0.04 |

Over a full day of coding (50-100 prompts), these differences compound. A developer using Opus for everything might spend $15-30 per day. The same work done mostly on Sonnet with targeted Opus usage might cost $5-8.

## Decision Flowchart

When deciding which model to use, ask:

1. **Does the task span 5+ files or require deep architectural reasoning?**
   Yes -> Opus. No -> continue.

2. **Is it a routine coding task (feature, bug fix, tests, docs)?**
   Yes -> Sonnet. No -> continue.

3. **Is it a simple lookup, single-line change, or boilerplate generation?**
   Yes -> Haiku. No -> Sonnet (safe default).

## Model-Specific Prompting Tips

### Opus
- Give it the full picture. Opus benefits from comprehensive context about the system architecture and constraints.
- Use plan mode. Let Opus design the approach before implementing.
- Worth the wait. Opus is slower but catches edge cases that Sonnet might miss.

### Sonnet
- Be direct. Sonnet works best with clear, actionable prompts.
- Provide file paths. Sonnet is more likely to need explicit file references than Opus.
- Good enough for most tasks. Resist the urge to upgrade to Opus unless Sonnet actually fails.

### Haiku
- Keep prompts focused. One clear question or one simple change per prompt.
- Do not ask Haiku to reason about complex multi-file interactions. It can, but accuracy drops.
- Ideal for the "read this and tell me X" pattern where you just need information extraction.

## Common Mistakes

- **Using Opus for everything.** Most tasks do not benefit from Opus-level reasoning. You pay 5x for the same result.
- **Using Haiku for complex refactoring.** Saving money on the prompt but spending more time fixing the output is not a real saving.
- **Never switching models.** The best developers switch models 3-5 times per day based on task complexity.
- **Optimizing model choice before optimizing prompts.** A well-structured Sonnet prompt beats a vague Opus prompt every time. Get your prompting right first, then tune model selection.
