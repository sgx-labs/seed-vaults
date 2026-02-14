---
title: "How Does the Reflexion Pattern Enable Self-Improving Agents?"
tags: [reflexion, self-correction, learning, memory, architecture]
content_type: research
domain: engineering
---

# How Does the Reflexion Pattern Enable Self-Improving Agents?

## The Question

How can agents learn from their own failures without retraining the model?

## What Reflexion Is

Reflexion extends ReAct by adding a self-evaluation step and persistent memory. After each attempt, the agent critiques its own performance, stores insights, and uses those insights on the next try.

```
Attempt 1: Try → Fail → Reflect ("I used the wrong API endpoint")
Attempt 2: Try (using insight from Attempt 1) → Fail → Reflect ("Auth header missing")
Attempt 3: Try (using insights from Attempts 1+2) → Succeed
```

The key innovation: reflections are stored in memory and injected into the prompt on subsequent attempts. The agent accumulates working knowledge without any model fine-tuning.

## The Three Components

1. **Actor** — The agent that attempts the task (standard ReAct agent)
2. **Evaluator** — Judges whether the attempt succeeded (can be automated tests, LLM-as-judge, or human)
3. **Reflection memory** — Stores natural language insights from failed attempts

## Implementation Pattern

```python
reflection_memory = []
max_attempts = 3

for attempt in range(max_attempts):
    # Include past reflections in the prompt
    context = "\n".join(reflection_memory) if reflection_memory else "No prior attempts."

    result = agent.execute(
        task=task,
        additional_context=f"Insights from prior attempts:\n{context}",
    )

    # Evaluate the result
    evaluation = evaluator.judge(result, expected_outcome)

    if evaluation.passed:
        return result

    # Generate reflection
    reflection = llm.generate(
        f"The attempt failed. Result: {result}\n"
        f"Evaluation: {evaluation.feedback}\n"
        f"What went wrong and what should be done differently?"
    )
    reflection_memory.append(f"Attempt {attempt + 1}: {reflection}")
```

## When to Use Reflexion

- **Tasks with verifiable outcomes.** You need an evaluator. Unit tests, validation checks, or LLM-as-judge.
- **Tasks where early failures are informative.** Each failure narrows the solution space.
- **Coding tasks.** The evaluator is the test suite — run tests, check output, reflect on failures.
- **Tasks worth retrying.** The cost of multiple attempts must be worth the improved success rate.

## When NOT to Use

- **No evaluator available.** Without a way to judge success, reflection is guesswork.
- **First attempt usually succeeds.** Reflexion overhead is wasted on easy tasks.
- **Time-critical tasks.** Multiple attempts mean multiple LLM calls and tool executions.
- **Non-deterministic environments.** If the environment changes between attempts, past reflections may mislead.

## Reflexion vs Simple Retry

| Aspect | Simple retry | Reflexion |
|--------|-------------|-----------|
| Memory | None — same prompt each time | Stores insights from each failure |
| Adaptation | Random — hope for different output | Directed — learns from specific mistakes |
| Cost | N identical attempts | N attempts with growing context |
| Success rate | Marginal improvement | Significant improvement per attempt |

## See Also

- `research/architecture/react-pattern.md` — The base pattern Reflexion extends
- `research/reliability/testing-agents.md` — Building evaluators
- `hub/memory.md` — Memory systems overview
