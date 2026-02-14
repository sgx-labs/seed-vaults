---
title: "Plan-and-Execute vs ReAct: When to Plan First"
tags: [plan-and-execute, ReAct, planning, architecture, patterns]
content_type: research
domain: engineering
---

# Plan-and-Execute vs ReAct: When to Plan First

## The Question

What is the difference between ReAct and Plan-and-Execute, and when should I use each?

## Core Difference

**ReAct:** Think one step at a time. Decide what to do next based on the last observation.
**Plan-and-Execute:** Create a full plan first. Then execute each step. Optionally re-plan if something changes.

```
ReAct:            Think → Act → Observe → Think → Act → Observe → ...
Plan-and-Execute: Plan → Execute Step 1 → Execute Step 2 → ... → Done
```

## When Plan-and-Execute Wins

- **Complex multi-step tasks.** When the task has 5+ steps and order matters.
- **Known problem structure.** You can describe the steps before starting (research → draft → review → publish).
- **Cost efficiency.** Planning once reduces total LLM calls vs. reasoning at every step.
- **Auditable workflows.** The plan is inspectable before execution begins.

**Example use case:** "Migrate the database schema, update the ORM models, fix all broken tests, and update the API documentation." This has a clear sequence. Planning it upfront prevents the agent from doing things out of order.

## When ReAct Wins

- **Exploratory tasks.** You do not know the steps until you start investigating.
- **Dynamic environments.** The next step depends on what you discover.
- **Simple tasks.** Planning overhead is not worth it for 1-3 step tasks.
- **Tool-heavy interaction.** The agent needs to search, read, and react to findings.

**Example use case:** "Find and fix the bug causing the 500 error in the checkout flow." You cannot plan the fix until you find the bug.

## Hybrid Approach

The most effective production pattern is often a hybrid:

```
1. Create high-level plan (3-5 steps)
2. Execute each step using ReAct (think-act-observe within each step)
3. After each step, check if the plan needs updating
4. Re-plan if the situation has changed
```

This gives you the benefits of planning (structure, auditability) with the flexibility of ReAct (adaptation within steps).

## Implementation Pattern

```python
# Hybrid: Plan then ReAct per step
plan = llm.generate("Create a step-by-step plan for: " + task)

for step in plan.steps:
    result = react_agent.execute(
        task=step.description,
        context=plan.overall_context,
        tools=available_tools,
    )
    if result.requires_replan:
        plan = llm.generate("Update the plan given: " + result.summary)
```

## Decision Matrix

| Factor | Favor ReAct | Favor Plan-and-Execute |
|--------|------------|----------------------|
| Task clarity | Unclear, exploratory | Well-defined steps |
| Step count | 1-3 steps | 5+ steps |
| Step dependencies | Independent | Ordered, dependent |
| Environment | Dynamic, changing | Stable, predictable |
| Cost sensitivity | Lower priority | Fewer LLM calls preferred |

## See Also

- `research/architecture/react-pattern.md` — ReAct deep dive
- `research/architecture/reflexion-pattern.md` — Adding self-correction
- `hub/architecture.md` — Architecture overview
