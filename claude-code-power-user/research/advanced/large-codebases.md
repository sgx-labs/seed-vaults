---
title: "Working with Large Codebases"
tags: [large-codebase, monorepo, architecture, context-management, advanced, subdirectory-claude-md, glob-grep]
content_type: research
domain: engineering
---

# Working with Large Codebases

## The Question

How do you use Claude Code effectively when the codebase is too large to fit in context -- hundreds of files, multiple packages, thousands of lines?

## The Fundamental Constraint

Claude Code cannot read your entire codebase. A 100K-line project would consume the entire context window just to load, leaving no room for the actual conversation. Claude must work with a subset of files at any time.

This means your job shifts from "writing code" to "pointing Claude at the right code." The better you are at narrowing scope, the better Claude performs.

## Strategy 1: Architecture Map in CLAUDE.md

The single highest-leverage thing you can do for a large codebase is describe its structure in `CLAUDE.md`. Claude reads this file at the start of every session.

```markdown
# CLAUDE.md

## Architecture

This is a Go monorepo with three main packages:

- `cmd/server/` — HTTP server, routes, middleware
- `internal/store/` — Database layer (PostgreSQL), all SQL queries
- `internal/auth/` — Authentication: JWT tokens, OAuth providers, sessions
- `internal/worker/` — Background job processing (Redis-backed)
- `pkg/shared/` — Shared types, utilities, constants
- `web/` — React frontend (Vite, TypeScript)

## Key Patterns

- All database queries are in `internal/store/`. No SQL outside this package.
- API handlers are in `cmd/server/handlers/`. One file per resource.
- Tests live next to the code they test (*_test.go, *.test.ts).
- Environment config is loaded in `cmd/server/config.go`.

## Build

- Backend: `make build` (requires Go 1.22+)
- Frontend: `cd web && npm run build`
- Tests: `make test` (backend) / `cd web && npm test` (frontend)
```

This takes 5 minutes to write and saves Claude thousands of tokens of exploration on every session. Without it, Claude greps blindly. With it, Claude goes directly to the right package.

## Strategy 2: Point to Specific Files

Always provide file paths when you know them:

```
Bad:  "Fix the bug in the order processing logic."
Good: "Fix the nil pointer in internal/store/orders.go around line 150.
       It happens when the order has no line items."
```

The bad prompt forces Claude to search the entire codebase for "order processing logic." The good prompt sends Claude directly to the file.

### When You Do Not Know the File

Ask Claude to find it, but narrow the search:

```
"Search internal/store/ for the function that calculates order totals."
```

Scoping the search to a directory is much faster than searching everything.

## Strategy 3: Use Explore Subagents

For research tasks in large codebases, launch Explore subagents (see the subagents note for details). Each subagent investigates one area and returns a summary, keeping your main context clean.

```
You: "I need to understand how payments work before making changes.
      Use explore subagents to research:
      1. The payment processing flow in internal/payments/
      2. The webhook handlers in cmd/server/handlers/webhooks.go
      3. The payment-related database queries in internal/store/
      Summarize what each one does."
```

Three subagents run in parallel. Your context gets three compact summaries instead of the contents of every file in those directories.

## Strategy 4: Glob and Grep Before Editing

Before asking Claude to modify code, ask it to search first:

```
You: "Before making changes, find all files that import the
      OrderService and list them."
```

This produces a concrete list you can review. Then:

```
You: "Good. Now update the OrderService constructor in all those
      files to accept a logger parameter."
```

The two-step approach prevents Claude from missing references.

### Useful Search Patterns

```
"Grep for all TODO comments in internal/"
"Find all files that match *_test.go in the store package"
"Search for every function that returns an error in internal/auth/"
"List all files modified in the last commit"
```

## Strategy 5: Subdirectory CLAUDE.md Files

For monorepos, put a `CLAUDE.md` in each package or service directory. Claude reads the nearest `CLAUDE.md` based on the working directory.

```
mymonorepo/
  CLAUDE.md                    ← top-level architecture overview
  services/
    auth/
      CLAUDE.md                ← auth service specifics
    payments/
      CLAUDE.md                ← payment service specifics
  packages/
    shared-types/
      CLAUDE.md                ← shared types conventions
```

### What Goes in a Subdirectory CLAUDE.md

```markdown
# services/payments/CLAUDE.md

## This Service
Handles payment processing via Stripe. Runs as a separate service
on port 8082. Communicates with the auth service via gRPC.

## Key Files
- handler.go — HTTP handlers for payment endpoints
- stripe.go — Stripe API client wrapper
- models.go — Payment, Refund, and Invoice types
- worker.go — Async job processor for webhooks

## Testing
Run `go test ./...` from this directory.
Uses test fixtures in testdata/.
Stripe calls are mocked via the StripeMock interface.

## Common Tasks
- Adding a new payment method: See handler.go for the pattern
- Adding a webhook handler: See worker.go ProcessWebhook()
```

## Strategy 6: Compact Aggressively

In long sessions on large codebases, context fills fast. Use `/compact` proactively:

```
You: [after a research phase] "/compact"
You: "Now that we understand the architecture, let's implement
      the changes."
```

Compacting after research preserves the conclusions while discarding the raw exploration. This is especially important when you used the main context (not subagents) for research.

### When to Compact

- After reading many files for context
- After a long planning discussion
- Before starting a new task in the same session
- When Claude starts repeating itself or forgetting earlier context

## Strategy 7: Incremental Changes

Do not try to refactor an entire large codebase in one prompt. Break it into stages:

```
Session 1: "Refactor the store package to use the new query builder.
            Start with orders.go and orders_test.go only."

Session 2: "Continue the query builder migration. Do users.go and
            users_test.go next, following the same pattern as orders.go."

Session 3: "Finish the migration with products.go and inventory.go."
```

Each session has a narrow scope. Tests pass at every stage. If something goes wrong, you only need to revert one file, not twenty.

## Anti-Patterns

### Dumping the Whole Codebase

```
Bad: "Here's my entire project structure. Now fix the bug."
```

Do not paste large directory trees or file contents into the chat. Let Claude read what it needs.

### Vague Scope

```
Bad: "Improve the error handling in the project."
```

This prompt on a large codebase will either produce a massive change set or Claude will pick a random subset. Always scope to specific packages or files.

### Skipping Tests

```
Bad: "Rename UserService to AccountService across the monorepo."
     [does not run tests]
```

In a large codebase, a rename can break things in unexpected places. Always run tests after multi-file edits, even if the changes look straightforward.

### Ignoring CLAUDE.md

If you are spending time every session explaining where things are, write it down in `CLAUDE.md`. The 10 minutes you spend writing the architecture overview saves hours across future sessions.

## Quick Reference

| Codebase Size | Strategy |
|--------------|----------|
| < 20 files | Claude can explore freely. Minimal guidance needed. |
| 20-100 files | Write a CLAUDE.md with architecture overview. Provide file paths. |
| 100-500 files | Use subagents for research. Subdirectory CLAUDE.md files. Compact often. |
| 500+ files / monorepo | All of the above plus incremental changes, staged refactors, and heavy use of Glob/Grep before editing. |
