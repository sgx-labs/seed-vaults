---
title: "Prompt Templates by Task Type for Claude Code"
tags: [prompts, templates, patterns, tasks, workflow]
content_type: research
domain: engineering
---

# Prompt Templates by Task Type for Claude Code

## The Core Idea

Different task types need different information upfront. A bug fix needs an error message and reproduction steps. A feature request needs a target location and a pattern to follow. Using the right template for the right task eliminates back-and-forth and produces better results on the first pass.

## 1. Feature Development

### Template

```
Add [feature] to [location].
Follow the pattern in [existing example].
[Specific requirements].
[Constraints].
```

Pointing to an existing example in your codebase is more effective than explaining architecture in prose. Claude reads the example and replicates the conventions.

### Concrete Example

```
Add a PATCH /api/users/:id endpoint to src/api/users.ts.
Follow the pattern of the existing PUT endpoint in the same file.
Accept partial updates — only fields present in the body should be changed.
Validate that email, if provided, is unique. Return 409 if duplicate.
Add a test in src/api/__tests__/users.test.ts covering:
  - Partial update (just name)
  - Email uniqueness check
  - Updating a non-existent user (404)
```

**Anti-pattern:** "I need a way to partially update users. Maybe PATCH?" -- invites a design conversation when you already know what you want.

## 2. Bug Fixing

### Template

```
Error: [exact error message]
File: [file path and line number if known]
Reproduction: [steps or conditions that trigger it]
Expected: [what should happen]
Actual: [what happens instead]
```

Error messages and stack traces are the highest-signal input. Claude often identifies the root cause from the error alone.

### Concrete Example

```
Error: "SQLITE_CONSTRAINT: UNIQUE constraint failed: notes.path"
File: internal/store/notes.go:89
Reproduction: Index a vault, rename a file, re-index the vault.
Expected: The renamed file gets a new row, old row is removed.
Actual: Insert fails because the old row with the original path still exists.
```

**Anti-pattern:** "The indexer is broken when I rename files." -- no error, no file, no steps. Claude guesses.

## 3. Refactoring

### Template

```
Extract [what] from [source] into [destination].
Keep [existing API/interface] unchanged.
[Specific things that must not break].
Run tests after to verify nothing is broken.
```

Stating what must stay unchanged gives Claude guard rails. Asking it to run tests catches regressions.

### Concrete Example

```
Extract the SQL query building logic from src/db/users.ts into
src/db/query-builder.ts. Handle SELECT, INSERT, UPDATE.
Keep all existing function signatures in users.ts unchanged —
they should import and use QueryBuilder internally.
Update orders.ts and products.ts to also use it.
Run the full test suite after changes.
```

**Anti-pattern:** "The database code is messy. Clean it up." -- subjective. Be specific about the target structure.

## 4. Writing Tests

### Template

```
Write tests for [function/module] in [test file location].
Cover these cases:
- [Happy path case]
- [Edge case]
- [Error case]
- [Boundary case]
Use [testing framework/pattern] consistent with existing tests.
```

Without an explicit case list, Claude tends to write happy-path tests and miss edge cases.

### Concrete Example

```
Write tests for validateConfig() in src/config/validate.ts.
Put them in src/config/__tests__/validate.test.ts.
Cover: valid config, missing "apiKey" (throws ConfigError),
invalid port (negative, zero, >65535), empty "host",
extra unknown fields (ignored), null input.
Follow the style in src/auth/__tests__/token.test.ts.
```

**Anti-pattern:** "Write tests for the config module." -- which functions? What edge cases? Claude writes generic happy-path tests.

## 5. Documentation

### Template

```
Document [module/function/system] in [file location].
Cover:
- [What it does / purpose]
- [Setup / installation / prerequisites]
- [API / interface / usage]
- [Examples]
- [Known limitations or gotchas]
Audience: [who will read this]
```

Specifying sections and audience prevents a wall of unfocused text.

### Concrete Example

```
Document the caching module in src/cache/ at src/cache/README.md.
Cover: purpose, setup (env vars for TTL and max size), API (all
exports with types), examples (cache a response, invalidate, clear),
gotchas (memory limits, thread safety).
Audience: developers onboarding to the codebase. Under 150 lines.
```

**Anti-pattern:** "Document the cache." -- where? For whom? Covering what?

## 6. Code Review

### Template

```
Review [file or PR] for [specific concerns].
Focus on:
- [Area 1]
- [Area 2]
- [Area 3]
Flag issues as: critical (must fix), warning (should fix), or nit (optional).
```

Open-ended reviews produce noise. Directing focus to specific concerns produces actionable findings.

### Concrete Example

```
Review src/api/payments.ts for security and correctness.
Focus on: input validation, SQL injection, auth checks,
error message leakage, race conditions in balance updates.
Flag issues as critical, warning, or nit.
```

**Anti-pattern:** "Review my code and tell me if it's good." -- good by what criteria?

## Quick Reference Table

| Task | Must Include | Nice to Include |
|------|-------------|-----------------|
| Feature | What, Where, Pattern to follow | Tests, Constraints |
| Bug Fix | Error message, File path | Reproduction steps, Expected vs Actual |
| Refactor | Source, Destination, What stays unchanged | Run tests after |
| Testing | Function, Cases to cover | Test file location, Framework |
| Documentation | Module, Sections, Audience | Line limit, Examples |
| Review | File/PR, Focus areas | Severity classification |

## Combining Templates

Real prompts often combine types. A feature + testing prompt:

```
Add rate limiting middleware to src/middleware/rateLimit.ts.
100 req/min per IP. Return 429 with Retry-After when exceeded.
Follow the pattern in src/middleware/auth.ts.
Write tests covering: under limit, at limit, over limit (429), counter reset.
```

One prompt, clear expectations, testable output.
