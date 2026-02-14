---
title: "How Do I Implement Agent Handoffs and Delegation?"
tags: [handoffs, delegation, multi-agent, transfer, orchestration]
content_type: research
domain: engineering
---

# How Do I Implement Agent Handoffs and Delegation?

## The Question

How does one agent transfer work to another agent, and what context should it pass along?

## Handoff Types

### 1. Full Transfer

The originating agent completely hands off the task. The receiving agent takes over.

```
Agent A: "I've identified this is a database issue. Transferring to the DB specialist."
→ Full conversation context transfers to Agent B
→ Agent A exits. Agent B is now the active agent.
```

**Use when:** The task has left Agent A's domain entirely.

### 2. Delegation (Task + Return)

Agent A assigns a subtask to Agent B, waits for the result, then continues.

```
Agent A: "I need the test results for this module."
→ Agent B runs tests, returns results
→ Agent A receives results and continues its work
```

**Use when:** Agent A needs specialized work done but remains in control.

### 3. Parallel Fan-Out

Agent A spawns multiple agents simultaneously, then merges results.

```
Agent A: "Research these three topics in parallel."
→ Agent B, C, D each research one topic
→ Agent A collects and synthesizes all results
```

**Use when:** Independent subtasks can be parallelized for speed.

## What to Pass in a Handoff

**Always include:**
- Clear task description (what the receiving agent should do)
- Relevant context (findings so far, constraints, requirements)
- Expected output format (what the originating agent needs back)

**Never include:**
- Full conversation transcript (too much noise, too many tokens)
- Internal reasoning (the receiving agent has its own system prompt)
- Irrelevant context from earlier in the session

## Handoff Context Template

```json
{
  "task": "Review the authentication module for SQL injection vulnerabilities",
  "context": {
    "files_to_review": ["src/auth/login.py", "src/auth/middleware.py"],
    "known_issues": ["User input in line 42 is not sanitized"],
    "constraints": ["Do not modify the public API surface"]
  },
  "expected_output": {
    "format": "list of findings with severity and suggested fixes",
    "max_items": 10
  }
}
```

## Framework Support

| Framework | Handoff mechanism |
|-----------|------------------|
| Claude Agent SDK | Task tool (subagents) |
| LangGraph | Edge routing to different nodes |
| CrewAI | `delegate()` method on agents |
| OpenAI Agents SDK | `transfer_to_agent()` function |

## Session Handoffs (Across Time)

A different but related pattern: handing off between sessions of the same agent. This is the memory problem. Tools like SAME solve it by writing structured session handoff notes that the next session reads at startup.

```markdown
## Session Handoff
- **Completed:** Fixed auth middleware, added input validation
- **Pending:** Database migration for new user fields
- **Blockers:** Waiting on API key for the payment provider
```

## See Also

- `research/architecture/multi-agent-communication.md` — Communication patterns
- `research/architecture/state-management.md` — State management
- `research/memory/session-handoffs.md` — Cross-session handoffs
