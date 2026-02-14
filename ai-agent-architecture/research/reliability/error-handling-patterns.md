---
title: "How Do I Handle Agent Errors and Hallucinations in Production?"
tags: [errors, hallucinations, production, recovery, error-handling]
content_type: research
domain: engineering
---

# How Do I Handle Agent Errors and Hallucinations in Production?

## The Question

What error handling patterns prevent agent failures from becoming user-facing incidents?

## Error Categories

| Category | Example | Strategy |
|----------|---------|----------|
| Transient | API rate limit, network timeout | Retry with backoff |
| Model error | Hallucinated tool call, bad JSON | Validate + re-prompt |
| Tool failure | External service down | Fallback or graceful degradation |
| Logic error | Agent stuck in a loop | Max iterations + escape hatch |
| Safety error | Agent attempts forbidden action | Guardrail blocks + logs |

## Pattern 1: Retry with Exponential Backoff

For transient failures (rate limits, timeouts, 5xx errors):

```python
import time

def call_with_retry(fn, max_retries=3, base_delay=1.0):
    for attempt in range(max_retries):
        try:
            return fn()
        except (RateLimitError, TimeoutError, ServerError) as e:
            if attempt == max_retries - 1:
                raise
            delay = base_delay * (2 ** attempt)  # 1s, 2s, 4s
            time.sleep(delay)
```

Add jitter to prevent thundering herd when multiple agents retry simultaneously.

## Pattern 2: Fallback Model

When the primary model fails, try a cheaper/different model:

```python
FALLBACK_CHAIN = ["claude-opus-4-6", "claude-sonnet-4-5-20250929", "claude-haiku-4-5-20251001"]

def generate_with_fallback(prompt, tools):
    for model in FALLBACK_CHAIN:
        try:
            return llm.generate(prompt, model=model, tools=tools)
        except ModelUnavailableError:
            continue
    raise AllModelsUnavailableError("No models available")
```

## Pattern 3: Output Validation

Never trust LLM output blindly. Validate before acting.

```python
def validate_tool_call(tool_name, params):
    schema = get_tool_schema(tool_name)
    if not schema:
        return {"error": f"Unknown tool: {tool_name}"}

    errors = jsonschema.validate(params, schema)
    if errors:
        return {"error": f"Invalid params: {errors}", "expected": schema}

    return None  # Valid
```

## Pattern 4: Circuit Breaker

Stop calling a broken service after repeated failures:

```python
class CircuitBreaker:
    def __init__(self, failure_threshold=5, reset_timeout=60):
        self.failures = 0
        self.threshold = failure_threshold
        self.reset_timeout = reset_timeout
        self.last_failure = None
        self.state = "closed"  # closed = normal, open = blocking

    def call(self, fn):
        if self.state == "open":
            if time.time() - self.last_failure > self.reset_timeout:
                self.state = "half-open"
            else:
                raise CircuitOpenError("Service temporarily unavailable")

        try:
            result = fn()
            self.failures = 0
            self.state = "closed"
            return result
        except Exception as e:
            self.failures += 1
            self.last_failure = time.time()
            if self.failures >= self.threshold:
                self.state = "open"
            raise
```

## Pattern 5: Graceful Degradation

When a non-critical service fails, continue without it:

```python
def get_agent_context():
    context = {"system_prompt": SYSTEM_PROMPT}

    try:
        context["memory"] = memory_service.retrieve(query)
    except MemoryServiceError:
        context["memory"] = "Memory service unavailable. Proceeding without historical context."

    return context
```

The agent works with reduced capability rather than failing entirely.

## See Also

- `hub/reliability.md` — Reliability overview
- `research/reliability/rate-limits-api-failures.md` — Rate limit handling
- `research/reliability/guardrail-implementation.md` — Guardrails
