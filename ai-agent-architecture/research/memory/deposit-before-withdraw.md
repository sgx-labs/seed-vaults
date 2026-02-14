---
title: "The 'Deposit Before You Withdraw' Principle for Agent Memory"
tags: [memory, write-side, discipline, patterns, knowledge-management]
content_type: research
domain: engineering
---

# The "Deposit Before You Withdraw" Principle for Agent Memory

## The Question

Why do most agent memory systems fail, and what principle makes them work?

## The Problem

Most memory systems focus on retrieval. They build sophisticated vector search, hybrid ranking, and context injection — the READ side. But retrieval quality depends entirely on what was written. Garbage in, garbage out.

The hard problem is not search. The hard problem is deciding **what to save**.

## The Principle

> Deposit Before You Withdraw: An agent that never writes to its memory has nothing to retrieve. The write side is the unique value.

This means every session should produce deposits:
- Decisions made and their rationale
- Session summaries (what was done, what is pending)
- Errors encountered and how they were resolved
- Key findings from research or debugging

## What Makes a Good Deposit

### High-Value Deposits

```markdown
## Decision: Use JWT Over Session Cookies
- Chose JWT for stateless auth. Simpler to scale across services.
- Considered session cookies but rejected due to server-side state management overhead.
- Implementation: RS256 signing, 15-minute expiry, refresh token rotation.
```

This is searchable, has context, and saves future sessions from re-deliberating.

### Low-Value Deposits

```markdown
## Conversation Log
User: How should we handle auth?
Agent: Let me think about this...
Agent: We could use JWT or session cookies...
[500 more lines of deliberation]
```

This is noise. The deliberation process is not useful — only the conclusion matters.

## The Write-Side Discipline

### What to Write

| Type | When to write | Example |
|------|--------------|---------|
| Decision | When a choice is made | "Chose PostgreSQL over MongoDB for..." |
| Session handoff | At session end | "Completed X, pending Y, blocked on Z" |
| Error resolution | When a bug is fixed | "Root cause was X. Fixed by Y." |
| Key finding | When something is discovered | "Rate limit is 100 RPM, not 500 as documented" |
| Entity update | When state changes | "API now at v3. Breaking change: auth header format" |

### What NOT to Write

| Type | Why not |
|------|---------|
| Full transcripts | Too noisy, too large |
| Intermediate reasoning | Only the conclusion matters |
| Obvious facts | Retrievable from code/docs directly |
| Speculative plans | They change; only record what was actually done |
| Raw tool outputs | Summarize instead |

## Automation

The best write-side discipline is automated so the agent does not have to remember:

```
SessionStart hook  → Loads last handoff + pinned notes
UserPromptSubmit   → Surfaces relevant knowledge
Stop hook          → Writes session handoff automatically
Decision command   → Logs decisions with rationale
```

With hooks handling the lifecycle, write-side discipline becomes structural rather than behavioral.

## See Also

- `hub/memory.md` — Memory overview
- `research/memory/persistent-memory-patterns.md` — Memory implementation
- `research/memory/session-handoffs.md` — Session handoff patterns
