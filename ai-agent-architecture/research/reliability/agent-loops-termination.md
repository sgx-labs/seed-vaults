---
title: "How Do I Prevent Agents from Getting Stuck in Loops?"
tags: [loops, termination, stuck, infinite-loop, reliability]
content_type: research
domain: engineering
---

# How Do I Prevent Agents from Getting Stuck in Loops?

## The Question

How do you detect and prevent infinite loops, repetitive behavior, and stuck agents?

## Why Agents Get Stuck

| Cause | Example | Symptom |
|-------|---------|---------|
| No stop condition | Agent keeps searching for "better" results | Never returns |
| Circular reasoning | Agent re-discovers the same information | Repeating tool calls |
| Error loop | Tool fails, agent retries same call | Identical failures |
| Insufficient context | Agent cannot find what it needs | Random tool calls |
| Ambiguous task | Agent does not know when "done" means done | Keeps refining |

## Prevention Strategies

### 1. Hard Iteration Limit

The simplest and most important guard:

```python
MAX_ITERATIONS = 25

for i in range(MAX_ITERATIONS):
    response = agent.step()
    if response.stop_reason == "end_turn":
        return response
    if i == MAX_ITERATIONS - 1:
        return force_summarize(agent, "Max iterations reached")
```

Never run an agent loop without a hard limit. Even if you think the task will finish in 5 turns, set a limit at 25.

### 2. Repetition Detection

Track tool calls and detect patterns:

```python
class RepetitionDetector:
    def __init__(self, window=5, threshold=3):
        self.history = []
        self.window = window
        self.threshold = threshold

    def check(self, tool_name: str, params: dict) -> bool:
        call_sig = f"{tool_name}:{json.dumps(params, sort_keys=True)}"
        self.history.append(call_sig)

        # Check last N calls for repetition
        recent = self.history[-self.window:]
        if recent.count(call_sig) >= self.threshold:
            return True  # Agent is repeating itself
        return False
```

When repetition is detected, intervene:
- Inject a message: "You have tried this approach 3 times. Try a different strategy."
- Force the agent to use a different tool
- Summarize progress and re-prompt

### 3. Cost-Based Termination

Stop when the cost exceeds the value:

```python
cost_tracker = CostTracker()

for i in range(MAX_ITERATIONS):
    response = agent.step()
    cost_tracker.add(response.input_tokens, response.output_tokens)

    if cost_tracker.total > MAX_COST_PER_TASK:
        return force_stop(agent, "Cost limit reached")
```

### 4. Progress Detection

Track whether the agent is making progress:

```python
class ProgressTracker:
    def __init__(self, stall_threshold=3):
        self.state_hashes = []
        self.stall_threshold = stall_threshold

    def update(self, state: dict) -> bool:
        state_hash = hash(json.dumps(state, sort_keys=True))
        self.state_hashes.append(state_hash)

        # If last N states are identical, agent is stalled
        recent = self.state_hashes[-self.stall_threshold:]
        if len(set(recent)) == 1 and len(recent) == self.stall_threshold:
            return False  # No progress
        return True  # Making progress
```

### 5. Escape Hatch Instruction

Add to the system prompt:

```
If you have attempted the same approach 3 times without success:
1. Stop and summarize what you have tried
2. Explain why each approach failed
3. Suggest what a human might try differently
4. Do NOT continue attempting the same approach
```

## See Also

- `research/reliability/error-handling-patterns.md` — Error handling
- `research/deployment/cost-optimization.md` — Cost management
- `hub/reliability.md` — Reliability overview
