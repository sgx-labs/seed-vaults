---
title: "Deployment — Production, Monitoring, and Scaling"
tags: [deployment, production, monitoring, scaling, observability, cost]
content_type: hub
domain: engineering
---

# Deployment — Production, Monitoring, and Scaling

Overview: This hub covers running AI agents in production including monitoring and observability with traces, metrics, and alerts, cost optimization strategies for token usage and model selection, scaling patterns with worker pools, async queues, and session pooling, agent debugging with structured logging and trace reconstruction, production readiness checklists, and quality measurement with pass@1 metrics, A/B testing, and benchmarks. Start here when deploying agents, setting up monitoring, controlling costs, or scaling to many users.

## The Production Gap

Getting an agent working locally is step one. Running it reliably in production is a different discipline entirely. Production agents need monitoring, cost controls, observability, and operational runbooks.

## Production Checklist

```
[ ] Error handling for every external call (LLM, tools, APIs)
[ ] Cost caps per session / per user / per day
[ ] Logging and tracing for every agent action
[ ] Guardrails for destructive operations
[ ] Fallback behavior when dependencies fail
[ ] Timeout limits for agent execution
[ ] Rate limit handling with backoff
[ ] Health checks and alerting
[ ] Graceful degradation paths
[ ] Runbook for common failure modes
```

## Monitoring What Matters

| Metric | Why it matters | Alert threshold |
|--------|---------------|----------------|
| Token usage per session | Cost control | > 2x average |
| Tool call success rate | Reliability signal | < 95% |
| Task completion rate | Core quality metric | < 90% |
| Latency (time to first response) | User experience | > 10s |
| Error rate by type | Identifies systemic issues | Any spike |
| Guardrail trigger rate | Safety signal | Any sustained increase |

## Cost Management

Agent costs compound quickly. A single agent session can use 100K+ tokens. At scale, this becomes the dominant cost.

**Cost levers:**
1. **Model selection** — Use cheaper models for simple subtasks (Haiku for classification, Opus for reasoning)
2. **Caching** — Cache tool results, embeddings, and repeated queries
3. **Context pruning** — Send only what the model needs, not everything you have
4. **Early termination** — Stop when the task is done, not when tokens run out
5. **Batch processing** — Aggregate small requests into fewer, larger calls

## Scaling Patterns

| Pattern | When | How |
|---------|------|-----|
| Horizontal | More users | Multiple agent instances behind a load balancer |
| Vertical | Harder tasks | Larger models, more context, longer timeouts |
| Async | Long-running tasks | Queue-based execution, webhook callbacks |
| Hybrid | Mixed workloads | Fast path (sync) + slow path (async) |

## Research Notes

- `research/deployment/production-checklist.md` — Complete production readiness guide
- `research/deployment/monitoring-observability.md` — What to monitor and how
- `research/deployment/cost-optimization.md` — Token cost management strategies
- `research/deployment/agent-debugging.md` — Tracing, logging, and replay
- `research/deployment/scaling-patterns.md` — Scaling agent systems

## See Also

- `hub/reliability.md` — Error handling, guardrails, and testing that underpin production readiness
- `hub/tools.md` — Tool design patterns that affect monitoring and debugging
- `hub/architecture.md` — Architecture decisions that affect scaling and cost
- `hub/frameworks.md` — Framework deployment options (LangGraph Platform, custom)
