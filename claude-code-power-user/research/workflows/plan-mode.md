---
title: "When and How to Use Plan Mode"
tags: [plan-mode, workflow, architecture, design, strategy, EnterPlanMode, refactoring]
content_type: research
domain: engineering
---

# When and How to Use Plan Mode

## What Plan Mode Does

Plan mode tells Claude to explore and design before writing code. Instead of immediately editing files, Claude will:

1. Read relevant files across the codebase
2. Analyze the existing architecture
3. Design an approach with specific files, functions, and changes
4. Present the plan for your approval
5. Wait for you to approve, adjust, or reject before implementing

This prevents the most expensive mistake in AI-assisted coding: implementing the wrong approach and having to undo it.

## How to Enter Plan Mode

### Explicitly

```
You: "Use plan mode to design the new caching layer."
You: "Plan how to refactor the database module before making changes."
```

### Via the Tool

Claude has an `EnterPlanMode` tool. When it detects a complex task, it may enter plan mode on its own. You can also ask directly:

```
You: "Enter plan mode."
```

### What Happens Inside

```
1. Claude calls EnterPlanMode
2. Claude explores: reads files, greps for patterns, checks tests
3. Claude writes a structured plan:
   - What changes are needed
   - Which files will be modified/created
   - The order of operations
   - Potential risks or trade-offs
4. Claude exits plan mode and presents the plan
5. You approve, adjust, or reject
6. If approved, Claude implements the plan
```

## When to Use Plan Mode

### Use It For

| Scenario | Why |
|----------|-----|
| New features spanning 3+ files | Prevents inconsistent implementations |
| Refactoring existing code | Need to understand all callers and dependencies |
| Changing interfaces or APIs | Must update all consumers |
| Database schema changes | Migrations, model updates, query changes cascade |
| Adding a new dependency or pattern | Should be consistent with existing patterns |
| Architectural decisions | Explore trade-offs before committing |

### Example: New Feature

```
You: "I need to add WebSocket support for real-time notifications.
      Use plan mode."

Claude: [reads server structure, existing routes, client code]
Claude: "Here's my plan:
  1. Add ws package to dependencies
  2. Create server/ws/hub.go — connection manager
  3. Create server/ws/client.go — per-connection handler
  4. Add /ws endpoint in server/routes.go
  5. Create notification types in shared/types.go
  6. Add client-side connection in frontend/lib/ws.ts
  7. Tests in server/ws/hub_test.go

  Trade-off: I'll use gorilla/websocket over nhooyr/websocket
  because your existing code uses gorilla for other things.

  Shall I proceed?"

You: "Yes, but use nhooyr/websocket — we're migrating away from gorilla."

Claude: [implements with the adjusted plan]
```

### Example: Refactoring

```
You: "The User struct is used in 20+ files and I need to split it
      into UserProfile and UserAuth. Plan this first."

Claude: [greps for all User references, reads each file]
Claude: "Here's the impact analysis:
  - 23 files reference User
  - 8 files only need profile fields
  - 12 files only need auth fields
  - 3 files need both
  Plan: Create UserProfile and UserAuth, update imports file by file,
  add a UserFull type alias for the 3 files that need both..."
```

## When NOT to Use Plan Mode

### Skip It For

| Scenario | Why |
|----------|-----|
| Single-line bug fixes | Overhead is not worth it |
| Typo corrections | Just fix it |
| Adding a log statement | Trivial change |
| Updating a config value | One file, one line |
| Running tests | No code changes |
| Git operations | Commits, pushes, status checks |
| Simple CRUD additions | Pattern is already established |

### Example: Just Do It

```
You: "The error message on line 42 of auth.go says 'invalid token'
      but it should say 'expired token'."

# No plan mode needed — Claude will just fix the string.
```

```
You: "Add a debug log at the start of ProcessOrder."

# One line, one file, no plan needed.
```

## How to Give Feedback on Plans

### Approve

```
You: "Looks good, go ahead."
You: "Approved. Implement it."
```

### Adjust

```
You: "Good plan, but change step 3 — use an interface instead of
      a concrete type."
You: "I like the approach but skip the migration for now. We'll
      handle that separately."
```

### Reject

```
You: "That's too complex. Let's try a simpler approach — just add
      a cache map in the existing handler."
```

### Ask for Alternatives

```
You: "What would this look like if we used Redis instead of
      in-memory caching?"
You: "Show me the trade-offs between approach A and approach B."
```

## Plan Mode Tips

1. **Be specific about scope.** "Plan the auth system" is too broad. "Plan adding JWT refresh tokens to the existing auth middleware" is right.

2. **Mention constraints.** "Plan this, but we can't add new dependencies" or "This needs to work with the existing database schema."

3. **Ask for impact analysis.** "How many files will this touch?" helps you gauge whether the plan is reasonable.

4. **Plans are cheap, implementations are expensive.** If you're unsure whether something needs plan mode, use it. A few minutes of planning saves hours of undoing.

5. **Combine with `/compact` after planning.** If the exploration phase generated a lot of output, compact before implementation to keep the context clean.

6. **Plan mode works with any model.** Opus gives more thorough plans. Sonnet is faster. For very large codebases, Opus is worth the extra time.
