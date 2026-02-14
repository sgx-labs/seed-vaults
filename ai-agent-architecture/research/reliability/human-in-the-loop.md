---
title: "How Do I Implement Human-in-the-Loop Approval?"
tags: [human-in-the-loop, approval, safety, confirmation, guardrails]
content_type: research
domain: engineering
---

# How Do I Implement Human-in-the-Loop Approval?

## The Question

When should agents pause for human approval, and how do you implement it without killing the user experience?

## The Approval Spectrum

```
Full autonomy ----- Notify after ----- Approve before ----- Human does it
    (risky)          (moderate)          (safest)            (no agent)
```

The right position depends on the action's reversibility and blast radius.

## When to Require Approval

| Action type | Approval needed? | Why |
|------------|-----------------|-----|
| Read files | No | Non-destructive |
| Write files | Usually no | Reversible (git) |
| Run tests | No | Non-destructive |
| Delete files | Yes | May not be reversible |
| Execute shell commands | Depends on command | Some are destructive |
| Send external messages | Yes | Not reversible |
| Deploy to production | Always yes | High blast radius |
| Financial transactions | Always yes | Not reversible |
| Modify permissions | Yes | Security implications |

## Implementation Patterns

### CLI Approval (Interactive)

```python
def approve_action(action: dict) -> bool:
    print(f"\nAgent wants to: {action['description']}")
    print(f"Details: {json.dumps(action['params'], indent=2)}")
    response = input("Approve? [y/N] ")
    return response.lower() == "y"
```

### Async Approval (Non-Blocking)

For long-running agents, pause and wait for approval via webhook or queue:

```python
async def request_approval(action: dict) -> bool:
    approval_id = await approval_queue.submit(action)
    # Agent pauses here. Human reviews in a dashboard.
    result = await approval_queue.wait_for_decision(approval_id, timeout=3600)
    return result.approved
```

### Batch Approval

Show the agent's proposed plan, approve the whole thing at once:

```python
def approve_plan(plan: list[dict]) -> list[dict]:
    print("Proposed plan:")
    for i, step in enumerate(plan):
        print(f"  {i+1}. {step['description']}")

    response = input("Approve all steps? [y/N/edit] ")
    if response.lower() == "y":
        return plan
    elif response.lower() == "edit":
        return edit_plan(plan)
    else:
        return []  # Rejected
```

### LangGraph Interrupts

```python
from langgraph.graph import StateGraph

def dangerous_action_node(state):
    # This node pauses for human input
    return {"action": state["proposed_action"], "status": "pending_approval"}

graph = StateGraph(AgentState)
graph.add_node("propose", propose_node)
graph.add_node("approve", dangerous_action_node)
graph.add_node("execute", execute_node)

# Interrupt before execution
app = graph.compile(interrupt_before=["approve"])
```

## UX Considerations

1. **Show context.** Do not just show "Delete file?" Show which file, why, and what happens if declined.
2. **Suggest a default.** If the agent is usually right, default to approve. If the action is risky, default to deny.
3. **Batch where possible.** 10 approval dialogs in a row is exhausting. Group related approvals.
4. **Allow "trust for session."** After approving the same type of action 3 times, offer to auto-approve for the rest of the session.
5. **Log all decisions.** Every approval and rejection should be auditable.

## See Also

- `research/reliability/guardrail-implementation.md` — Guardrail patterns
- `research/tools/tool-security.md` — Tool security
- `hub/reliability.md` — Reliability overview
