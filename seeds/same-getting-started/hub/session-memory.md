---
title: "Session Memory"
tags: [session, memory, handoff, decisions, context, continuity]
content_type: hub
domain: same-getting-started
---

# Session Memory

## The Session Problem

AI coding agents operate in sessions. When a session ends -- you close your terminal, switch projects, or the context window fills up -- everything the AI learned is gone. Tomorrow it won't remember:

- What you were working on
- What decisions you made
- What you tried that didn't work
- What you planned to do next

SAME solves this with three mechanisms: **handoffs**, **decisions**, and **pinned notes**.

## Handoffs

A handoff is a structured note that captures the state of a session. It typically includes:

- What was accomplished
- What's in progress
- What's planned next
- Key context the next session needs

### Creating Handoffs

At the end of a session, your AI can create a handoff using the `create_handoff` MCP tool:

```
# The AI writes something like:
## Session Handoff -- 2025-03-15

### Completed
- Implemented JWT authentication middleware
- Added rate limiting to API endpoints

### In Progress
- Database migration for user roles (schema written, not applied)

### Next Session
- Apply migration and test role-based access
- Write integration tests for auth flow

### Key Context
- Using RS256 for JWT signing (decision in decisions/auth-strategy.md)
- Rate limit set to 100 req/min per user
```

The next session starts by reading this handoff via `get_session_context`. Your AI immediately knows what happened and what to do.

### Automatic Context Loading

When a session starts, SAME's `get_session_context` tool returns:

1. **Pinned notes** -- Always-relevant context (project overview, coding standards)
2. **Latest handoff** -- What happened last session
3. **Git state** -- Current branch, recent commits, uncommitted changes

This happens automatically if you have SAME hooks installed. Your AI gets oriented in milliseconds.

## Decisions

Decisions are structured records of choices you've made. They prevent a common problem: your AI suggesting something you already decided against.

```bash
# The AI logs a decision via save_decision MCP tool:
# Title: "Use PostgreSQL over MySQL"
# Rationale: "Better JSON support, team familiarity, existing tooling"
# Alternatives: "MySQL (less JSON support), SQLite (not suitable for production scale)"
```

Future sessions can search for and find these decisions. Instead of re-debating database choice, your AI sees the decision and moves on.

## Pinned Notes

Some notes should always be in context. Mark them with `pinned: true`:

```yaml
---
title: "Project Coding Standards"
pinned: true
---
```

Good candidates for pinning:
- Project overview and architecture
- Coding standards and conventions
- Team agreements and workflows
- This seed's bootstrap.md (already pinned)

Keep pinned notes short. They're loaded every session, so they eat into your context budget.

## How It All Fits Together

```
Session 1                    Session 2                    Session 3
---------                    ---------                    ---------
Work on feature A            Read handoff from S1         Read handoff from S2
Make decision D1      -->    Know about D1 already  -->   Know about D1 and D2
Create handoff               Work on feature B            Continue feature B
                             Make decision D2
                             Create handoff
```

Each session builds on the last. Knowledge compounds instead of resetting.

## Next Steps

- Create your first vault and test handoffs: `hub/your-first-vault.md`
- Connect SAME to Claude Code for automatic context: `guides/connect-to-claude-code.md`
- Understand multi-agent handoffs: `research/agent-handoffs-explained.md`
