---
title: "Dealing with Slow Claude Code Responses"
tags: [performance, optimization, context-window, troubleshooting]
content_type: research
domain: engineering
---

# Dealing with Slow Claude Code Responses

Most performance issues come from bloated context, inefficient prompting, or API limits.

---

## Causes of Slow Responses

| Cause | Symptom | How to Confirm |
|---|---|---|
| Large context window | Progressively slower responses | Long session, many files read |
| Broad file scanning | Dozens of tool calls before acting | Watch tool calls scroll by |
| Rate limiting | Sudden pauses, 429 errors | Check verbose output |
| Network latency | Consistent delay on every response | Test with a simple prompt |
| Complex reasoning | One-off slow response | Other responses are fast |

---

## Diagnosis

**Slow on the first message of a fresh session?**
- Yes: Network or rate limiting. Check connection and API status.
- No: Context accumulation. Read on.

**Gets slower over the session?**
- Yes: Context filling up. Use `/compact` or new session.
- No: Claude is doing something expensive. Check what tools it is calling.

---

## Fix 1: Start a Fresh Session

The most effective fix. Every message, file read, and tool result accumulates. A session that started fast 90 minutes ago may carry 150K tokens of history.

```
/clear
```

Re-state your task concisely. Do not paste conversation history. Give the current state and what remains.

**When to start fresh:** More than an hour in, Claude repeating itself, responses noticeably slower, switching to an unrelated task.

---

## Fix 2: Use /compact

Summarizes earlier messages while preserving recent ones. Reduces token count without losing the thread.

**When to use:** Mid-task where starting fresh would lose context, after an exploratory phase before implementation, at first signs of slowdown.

**Limitation:** Compaction is lossy. Early details may be lost. Best when early conversation was exploratory and recent messages are the real work.

---

## Fix 3: Reference Specific Files

The biggest context sink is Claude reading files it does not need.

**Slow:**
```
Look at my codebase and find all authentication handling, then fix the token refresh bug.
```

**Fast:**
```
There's a token refresh bug in src/auth/refresh.ts around line 45.
The refresh token is not being validated before use. Fix it.
```

| Instead of... | Say... |
|---|---|
| "Look at the auth module" | "Read src/auth/middleware.ts" |
| "Find all API routes" | "Routes are in src/routes/index.ts" |
| "Check the tests" | "Run `npm test -- --testPathPattern=auth`" |
| "Look at everything related to users" | "User model: src/models/user.ts, controller: src/controllers/user.ts" |

---

## Fix 4: Use Subagents for Research

Subagents run in a separate context and return a summary, keeping your main context lean.

**Without subagent:** Claude reads 20+ files (40K+ tokens) into main context for a broad search, even though only 3 files matter for the actual edit.

**With subagent:**
```
Use a subagent to research how database connections are handled across the
codebase. Then use its findings to refactor the connection pool in src/db/pool.ts.
```

The subagent reads 20 files in its own context. Main context gets a ~500 token summary.

---

## Fix 5: Batch Operations

Multiple small requests are slower than one big request.

**Slow:**
```
Add error handling to src/handlers/users.ts.
```
(wait) `Now do src/handlers/posts.ts.` (wait) `Now src/handlers/comments.ts.`

**Fast:**
```
Add error handling to these three files using the same try/catch pattern:
- src/handlers/users.ts
- src/handlers/posts.ts
- src/handlers/comments.ts
```

One reasoning pass, three edits. Much faster than three round-trips.

---

## Fix 6: Match Model to Task

Toggle with `/model`. Simple tasks run faster on lighter models.

**Faster model works for:** Variable renaming, adding imports, boilerplate from a clear spec, formatting fixes, simple search-and-read.

**Full model needed for:** Architecture decisions, complex multi-system debugging, broad-context refactoring, business-logic tests.

---

## Performance Patterns at a Glance

| Pattern | Context Impact | Speed |
|---|---|---|
| "Fix bug in file X, line Y" | Minimal | Fast |
| "Find and fix all bugs in module X" | Moderate | Medium |
| "Look at everything and find problems" | Massive | Slow |
| One task per session | Controlled | Consistent |
| Five tasks in one session | Accumulating | Degrades |
| Subagent for research | Isolated | Fast in main |

---

## When Performance Drops Mid-Session

In order of likelihood:

1. **Context is full.** Accumulated history slows every response. Fix: `/compact` or new session.
2. **Claude entered a loop.** Retrying failures, re-reading files. Fix: Interrupt and redirect.
3. **Rate limit hit.** Burst of tool calls triggered throttling. Fix: Wait 30-60 seconds.
4. **API degradation.** Check status.anthropic.com. Fix: Wait and retry.

The first cause accounts for the vast majority of slowdowns. When in doubt, start a new session.

---

## Preventive Habits

- Start each task in a fresh session
- Give file paths, not descriptions
- Use `/compact` after exploratory phases
- Batch related edits into single prompts
- Use subagents for broad research
- Keep sessions under 60-90 minutes for complex work
- Interrupt if Claude is reading irrelevant files
