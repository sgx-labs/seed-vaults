---
title: "How Do I Carry State Between Agent Sessions?"
tags: [sessions, handoffs, state, continuity, memory]
content_type: research
domain: engineering
---

# How Do I Carry State Between Agent Sessions?

## The Question

How do you ensure an agent picks up where it left off in the next session?

## The Session Boundary Problem

When a session ends, everything in the context window is gone. The next session starts with a blank context. Without explicit state transfer, the agent will:
- Re-discover things it already found
- Re-make decisions it already made
- Ask questions it already asked
- Lose track of multi-session projects

## The Handoff Pattern

Write a structured summary at session end. Read it at session start.

### What to Include

```markdown
## Session Handoff — 2026-02-14

### Summary
Refactored the payment module. Separated payment gateway logic from order processing.
Added retry logic for failed charges.

### Completed
- Split PaymentService into PaymentGateway and OrderProcessor
- Added exponential backoff for gateway timeouts
- Updated unit tests for new class structure

### Pending
- Integration tests for retry scenarios
- Update API documentation for new endpoint structure
- Performance testing under load

### Decisions Made
- Chose exponential backoff over linear retry (see decisions/payment-retry.md)
- Kept synchronous processing for now (async migration deferred to next sprint)

### Blockers
- Staging environment payment gateway returns 503 intermittently
```

### What NOT to Include

- Full conversation transcript (too long, too noisy)
- Implementation details the agent can re-read from code
- Speculative plans that were not executed
- Emotional tone or narrative ("I struggled with...")

## Automation Patterns

### Hook-Based (Claude Code + SAME)

```json
{
  "hooks": {
    "SessionStart": [{
      "type": "command",
      "command": "same hook session-start"
    }],
    "Stop": [{
      "type": "command",
      "command": "same hook stop"
    }]
  }
}
```

SessionStart loads the latest handoff. Stop saves a new one. The agent does not need to remember to do this — it happens automatically.

### File-Based (Manual)

Save to a known location. Read it in the system prompt.

```python
HANDOFF_PATH = ".agent/last_session.md"

# At session start
if os.path.exists(HANDOFF_PATH):
    handoff = read_file(HANDOFF_PATH)
    system_prompt += f"\n\nPrevious session context:\n{handoff}"

# At session end
write_file(HANDOFF_PATH, generate_handoff_summary())
```

## The "Stop Is Best-Effort" Problem

In many frameworks, the session-end hook is not guaranteed to fire (crashes, timeouts, user closes terminal). Design for this:

1. **Write incrementally.** Update handoff notes during the session, not just at the end.
2. **Crash recovery.** At session start, check if the last session ended cleanly. If not, reconstruct state from available logs.
3. **Idempotent handoffs.** Writing the same handoff twice should be harmless.

## See Also

- `research/memory/persistent-memory-patterns.md` — Long-term memory
- `research/architecture/state-management.md` — State management
- `hub/memory.md` — Memory overview
