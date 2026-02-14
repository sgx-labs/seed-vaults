---
title: "Reliability — Error Handling, Guardrails, and Testing"
tags: [reliability, errors, guardrails, testing, safety, retries]
content_type: hub
domain: engineering
---

# Reliability — Error Handling, Guardrails, and Testing

## Why Reliability Is the Hard Problem

A demo agent that works 80% of the time is impressive. A production agent that fails 20% of the time is unusable. The gap between "works in demo" and "works in production" is almost entirely reliability engineering.

## The Reliability Stack

```
Layer 4: Guardrails      — prevent harmful/off-scope actions
Layer 3: Error Recovery   — handle failures gracefully
Layer 2: Validation       — catch bad outputs before they propagate
Layer 1: Prompt Design    — reduce errors at the source
```

Each layer catches what the layer below misses. Skip a layer and failures leak through.

## Error Handling Patterns

| Pattern | When to use | Example |
|---------|------------|---------|
| Retry with backoff | Transient API failures | Rate limit → wait → retry |
| Fallback model | Primary model unavailable | Opus fails → try Sonnet |
| Fallback strategy | Tool fails | API down → use cached data |
| Circuit breaker | Repeated failures | Stop calling broken service after N failures |
| Graceful degradation | Partial system failure | Memory unavailable → continue without context |

## Guardrail Categories

1. **Input guardrails** — Validate what goes into the agent (prompt injection detection, input sanitization)
2. **Output guardrails** — Validate what comes out (PII detection, format validation, hallucination checks)
3. **Action guardrails** — Control what the agent can do (tool allowlists, permission scoping, rate limits)
4. **Scope guardrails** — Keep the agent on task (topic boundaries, step limits, cost caps)

## Testing Agents

Agent testing is fundamentally different from software testing because outputs are stochastic.

| Test type | What it validates | Approach |
|-----------|------------------|----------|
| Unit tests | Individual tools work | Standard software testing |
| Integration tests | Agent + tools work together | Scripted scenarios with assertions |
| Eval harness | Agent quality at scale | Run N trials, measure pass rate |
| Red teaming | Safety and edge cases | Adversarial prompts, boundary testing |
| Regression tests | New changes do not break existing behavior | Golden dataset comparison |

## The Eval Mindset

Do not ask "did the agent get the right answer?" Ask "across 100 runs, how often does it get the right answer?" Agent quality is a distribution, not a binary.

## Research Notes

- `research/reliability/error-handling-patterns.md` — Production error handling strategies
- `research/reliability/guardrail-implementation.md` — Building effective guardrails
- `research/reliability/testing-agents.md` — How to test stochastic systems
- `research/reliability/eval-harness-design.md` — Building evaluation infrastructure
- `research/reliability/rate-limits-api-failures.md` — Handling rate limits gracefully
- `research/reliability/prompt-robustness.md` — Prompts that survive model updates
- `research/reliability/human-in-the-loop.md` — When and how to involve humans
