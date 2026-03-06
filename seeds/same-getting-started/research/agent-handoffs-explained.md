---
title: "Agent Handoffs Explained"
tags: [handoffs, multi-agent, coordination, session, continuity, decisions]
content_type: research
domain: same-getting-started
---

# Agent Handoffs Explained

## Why Handoffs Matter

Every AI session is temporary. When the session ends, the AI loses all context. Without a handoff, the next session starts from zero -- re-reading files, re-discovering the codebase, re-asking questions you already answered.

A handoff is a structured note that captures what happened and what comes next. It's the bridge between sessions.

## What a Good Handoff Contains

A handoff should answer five questions for the next session:

1. **What was accomplished?** -- Concrete deliverables, not vague summaries.
2. **What's in progress?** -- Work that was started but not finished.
3. **What's blocked?** -- Issues that need resolution before continuing.
4. **What's next?** -- The most important thing for the next session to do.
5. **Key context** -- Anything the next session needs to know that isn't in other notes.

## Creating Handoffs

SAME provides the `create_handoff` MCP tool. Your AI calls it at the end of a session:

```
create_handoff:
  summary: "Implemented rate limiting middleware"
  completed:
    - "Added token bucket rate limiter to API gateway"
    - "Configured 100 req/min per user default"
  in_progress:
    - "Integration tests for rate limiting (3 of 7 passing)"
  next_steps:
    - "Fix remaining 4 integration tests"
    - "Add rate limit headers to responses"
  context:
    - "Using golang.org/x/time/rate library"
    - "Rate limits stored in Redis, not in-memory"
```

The handoff gets saved to the `sessions/` directory with a timestamp.

## Reading Handoffs

At session start, `get_session_context` automatically loads the most recent handoff. The AI reads it and knows exactly where to pick up.

You can also search handoffs:

```bash
same search "rate limiting progress"
```

## Multi-Agent Workflows

When multiple AI agents work on the same codebase, handoffs become even more critical. SAME supports this with:

### File Claims

Agents can claim files they're working on to prevent conflicts:

```bash
same claim src/api/auth.go --agent backend-agent
```

Other agents see the claim and avoid editing that file.

### Agent Attribution

Notes and decisions can be attributed to specific agents:

```yaml
---
title: "Auth Middleware Design"
agent: backend-agent
---
```

This makes it clear who (which agent) produced what.

### Decision Tracking

When multiple agents are involved, decisions need to be visible to everyone. The `save_decision` tool logs decisions to a shared location that all agents can search:

```
save_decision:
  title: "Use Redis for rate limiting storage"
  rationale: "In-memory counters reset on restart. Redis persists across deploys."
  alternatives: "In-memory (simpler but volatile), PostgreSQL (too slow for per-request checks)"
```

## Handoff Best Practices

1. **Create handoffs at every session end.** Don't skip "because I'll remember." You won't.
2. **Be specific.** "Worked on auth" is useless. "Added JWT validation to /api/users endpoint, RS256 signing, refresh token rotation every 7 days" is useful.
3. **Include file paths.** The next session needs to know which files were touched.
4. **Mention what didn't work.** "Tried using middleware chaining but it broke error handling" saves the next session from repeating the experiment.
5. **Keep it under 50 lines.** A handoff should be scannable, not a novel.

## Next Steps

- Understand session memory in depth: `hub/session-memory.md`
- Set up SAME with Claude Code: `guides/connect-to-claude-code.md`
- See how decisions are tracked: `decisions/example-decision.md`
