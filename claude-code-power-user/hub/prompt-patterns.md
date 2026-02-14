---
title: "Prompt Patterns — Talking to Claude Code Effectively"
tags: [prompts, patterns, efficiency, communication, token-saving, batching, file-paths]
content_type: hub
domain: engineering
---

# Prompt Patterns — Talking to Claude Code Effectively

## Overview

Prompt patterns for Claude Code cover effective prompting strategies for feature development, bug fixing, refactoring, code review, and research tasks. Key patterns include the four-part formula (what, where, why, constraints), file path references over descriptions, batched operations for token savings, plan mode for complex tasks, and anti-patterns that waste tokens. This hub links to detailed guides on task-specific templates, model-specific prompting (Opus, Sonnet, Haiku), and common mistakes.

## Core Principle

Claude Code is a coding agent, not a chatbot. The more context and specificity you provide, the better the results. Think of it like pair programming — you wouldn't tell your pair "fix it" without context.

## The Best Prompts Include

1. **What** you want done (the goal)
2. **Where** in the code (file paths, function names)
3. **Why** (context that helps Claude make good decisions)
4. **Constraints** (things to avoid, patterns to follow)

## Prompt Patterns by Task

### Feature Development
```
Add input validation to the signup form in src/components/SignupForm.tsx.
Validate email format, password strength (8+ chars, 1 number, 1 special),
and display inline errors. Follow the existing error pattern in LoginForm.tsx.
```

### Bug Fixing
```
The checkout flow fails when the cart has more than 10 items.
Error: "TypeError: Cannot read property 'price' of undefined"
in src/utils/cart.ts:47. The issue started after the last commit.
```

### Refactoring
```
Refactor the user service to use the repository pattern.
Currently all database queries are inline in the route handlers.
Extract them to src/repositories/UserRepository.ts.
Keep the existing API contract unchanged.
```

### Research / Exploration
```
Explain how the authentication middleware works in this project.
Trace the flow from the login endpoint through token validation
to the protected route handler.
```

## Anti-Patterns

| Don't | Do instead |
|-------|-----------|
| "Fix the bug" | "Fix the null pointer in cart.ts:47 when items array is empty" |
| "Make it better" | "Reduce the API response time by adding caching to the user endpoint" |
| "Clean up this code" | "Extract the validation logic in signup.ts into a shared validator" |
| "Do everything" | "Start with the database migration, then update the models" |
| Multiple unrelated asks in one prompt | One focused request per prompt, or explicitly list numbered tasks |

## Token-Saving Patterns

1. **Reference files by path** — `Fix src/auth/middleware.ts:23` not "fix the auth file"
2. **Batch related changes** — "Update all API endpoints to use the new error format"
3. **Use plan mode for big tasks** — Prevents wasted implementation effort
4. **Don't explain what Claude can read** — It has your codebase, don't paste code into prompts
5. **Use slash commands** — `/commit`, `/review` are faster than typing instructions

## Research Notes

- `research/prompts/effective-prompts.md` — Detailed prompt writing guide
- `research/prompts/batching-operations.md` — How to batch for efficiency
- `research/prompts/task-specific-prompts.md` — Templates by task type
- `research/prompts/model-differences.md` — How prompting differs by model
- `research/prompts/common-mistakes.md` — Token-wasting mistakes to avoid

## See Also

- `hub/workflows.md` — Workflow patterns that complement good prompting
- `hub/troubleshooting.md` — When prompts produce unexpected results
- `hub/hooks.md` — Automatic context surfacing reduces manual prompting
- `hub/getting-started.md` — First prompts for new users
