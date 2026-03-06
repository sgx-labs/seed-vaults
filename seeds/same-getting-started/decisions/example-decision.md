---
title: "Example Decision"
tags: [decisions, example, template, format]
content_type: decision
domain: same-getting-started
---

# Example Decision

This is a sample decision document showing the format SAME uses when you call `save_decision`. Use this as a reference for logging your own project decisions.

## AD-XXX: Choose JWT over Session Cookies for Authentication

**Status:** Accepted
**Date:** 2025-03-15

We chose JWT (JSON Web Tokens) with RS256 signing for API authentication. JWTs are stateless -- the server doesn't need to store session data, which simplifies our architecture and makes horizontal scaling easier. We use refresh tokens with 7-day expiry to handle token rotation without forcing frequent re-authentication.

**Alternative considered:** Session cookies with a Redis session store. This would have required sticky sessions or a shared Redis instance, adding infrastructure complexity. Rejected because our API is consumed by mobile clients where cookies are less natural.

---

## How to Log Decisions with SAME

Your AI logs decisions automatically when you make project choices. Just tell your AI:

> "Let's go with PostgreSQL instead of MySQL."

The AI calls `save_decision` with:
- **Title:** What was decided
- **Rationale:** Why this choice was made
- **Alternatives:** What else was considered and why it was rejected

Decisions are saved to the `decisions/` directory and indexed by SAME. Future sessions can search for them:

```bash
same search "authentication decision"
same search "database choice"
```

This prevents your AI from re-suggesting options you've already evaluated and rejected.

## Related Notes

- Session memory and decisions: `hub/session-memory.md`
- Agent handoffs: `research/agent-handoffs-explained.md`
