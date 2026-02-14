---
title: "What Are the Cost Optimization Strategies for Agent Systems?"
tags: [cost, optimization, tokens, pricing, efficiency, model-selection, prompt-caching, batch-processing]
content_type: research
domain: engineering
---

# What Are the Cost Optimization Strategies for Agent Systems?

## The Question

How do you prevent agent costs from spiraling as you scale?

## Why Agent Costs Compound

A single agent task might use:
- 2K tokens for the system prompt
- 3K tokens for tool definitions
- 10K tokens for conversation (5 turns)
- 15K tokens for tool results
- 5K tokens for agent responses
- **Total: ~35K tokens per task**

At Claude Sonnet rates, that is roughly $0.15-0.25 per task. At 1,000 tasks/day, that is $150-250/day. At scale, this becomes the dominant cost.

## Cost Optimization Levers

### 1. Model Selection by Task

Use expensive models only when they add value:

```python
def select_model(task_type: str) -> str:
    if task_type in ("architecture_review", "complex_debugging"):
        return "claude-opus-4-6"        # $15/$75 per 1M tokens
    elif task_type in ("code_generation", "analysis"):
        return "claude-sonnet-4-5-20250929"  # $3/$15 per 1M tokens
    else:  # classification, formatting, simple tasks
        return "claude-haiku-4-5-20251001"   # $0.80/$4 per 1M tokens
```

Routing 60% of tasks to Haiku instead of Sonnet can cut costs by 70%.

### 2. Context Pruning

Send only what the model needs:

```python
# Bad: Send entire file
tool_result = read_entire_file("src/auth.py")  # 500 lines, 8K tokens

# Good: Send relevant section
tool_result = read_lines("src/auth.py", start=40, end=60)  # 20 lines, 300 tokens
```

Truncate tool results. Summarize large outputs. The agent rarely needs the full result.

### 3. Prompt Caching

Anthropic and OpenAI offer prompt caching — repeated system prompts cost less:

```python
response = client.messages.create(
    model="claude-sonnet-4-5-20250929",
    system=[{
        "type": "text",
        "text": SYSTEM_PROMPT,
        "cache_control": {"type": "ephemeral"}  # Cache this
    }],
    messages=messages,
)
# First call: full price. Subsequent calls: 90% cheaper for cached portion.
```

For agents with long system prompts and tool definitions, caching saves 30-50% on input tokens.

### 4. Caching Tool Results

Cache expensive tool results:

```python
from functools import lru_cache

@lru_cache(maxsize=100)
def search_codebase(query: str) -> list:
    # Expensive embedding search
    return vector_db.search(query, top_k=10)
```

Invalidate the cache when underlying data changes.

### 5. Early Termination

Stop when the task is done, not when the iteration limit is reached:

```python
MAX_ITERATIONS = 20

for i in range(MAX_ITERATIONS):
    response = agent.step()
    if response.task_complete:
        break  # Do not continue iterating
    if response.confidence > 0.95:
        break  # Good enough
```

### 6. Batch Processing

Combine multiple small requests into fewer, larger ones:

```python
# Bad: 10 separate API calls
for file in files:
    result = llm.generate(f"Summarize {file}")

# Good: 1 API call with all files
result = llm.generate(f"Summarize each of these files:\n{all_files_content}")
```

## Cost Monitoring

Track costs in real time and set alerts:

```python
class CostTracker:
    def __init__(self, daily_limit: float = 100.0):
        self.daily_total = 0.0
        self.daily_limit = daily_limit

    def record(self, input_tokens: int, output_tokens: int, model: str):
        cost = calculate_cost(input_tokens, output_tokens, model)
        self.daily_total += cost
        if self.daily_total > self.daily_limit * 0.8:
            alert("Approaching daily cost limit")
        if self.daily_total > self.daily_limit:
            raise CostLimitExceeded("Daily cost limit reached")
```

## See Also

- `hub/deployment.md` — Deployment overview
- `entities/context-window.md` — Token limits and management
- `entities/openrouter.md` — Multi-model routing for cost optimization
