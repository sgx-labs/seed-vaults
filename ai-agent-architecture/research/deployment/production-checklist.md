---
title: "What Does a Production-Ready Agent Need?"
tags: [production, checklist, deployment, readiness, operations, health-check, alerting, staged-rollout]
content_type: research
domain: engineering
---

# What Does a Production-Ready Agent Need?

## The Question

What separates a working prototype from a production-ready agent?

## The Production Readiness Checklist

### Reliability

```
[ ] Retry logic for all external calls (LLM, APIs, tools)
[ ] Fallback model chain (primary unavailable -> secondary -> tertiary)
[ ] Maximum iteration limit on agent loops
[ ] Timeout on every external call
[ ] Circuit breaker for repeatedly failing services
[ ] Graceful degradation when non-critical services are down
```

### Safety

```
[ ] Input validation and sanitization
[ ] Output validation before acting on results
[ ] Tool permission scoping (least privilege)
[ ] Human approval for destructive actions
[ ] Prompt injection detection
[ ] PII filtering on outputs
[ ] Cost cap per session / per user / per day
```

### Observability

```
[ ] Structured logging for every agent action
[ ] Distributed tracing across agent turns
[ ] Token usage tracking per session
[ ] Tool call success/failure metrics
[ ] Task completion rate monitoring
[ ] Latency tracking (time to first response, total session time)
[ ] Guardrail trigger rate monitoring
[ ] Error rate by type and severity
```

### Operations

```
[ ] Health check endpoint
[ ] Alerting rules for critical failures
[ ] Runbook for common failure modes
[ ] Rollback procedure for agent updates
[ ] On-call rotation with agent-specific knowledge
[ ] Incident response plan for agent misbehavior
```

### Performance

```
[ ] Response time under load (p50, p95, p99)
[ ] Concurrent session capacity
[ ] Memory usage under sustained load
[ ] Token cost per task (tracked over time)
[ ] Cache hit rate for repeated queries
```

## The Most Common Production Failures

| Failure | Root cause | Prevention |
|---------|-----------|-----------|
| Agent stuck in loop | No iteration limit | Max iterations + stuck detection |
| Unexpected cost spike | No cost controls | Session cost cap + alerting |
| Slow responses | No timeouts | Timeout on every external call |
| Silent data corruption | No output validation | Validate before writing/sending |
| Security incident | Excessive permissions | Least privilege + audit logging |
| Cascading failure | No circuit breakers | Circuit breaker per dependency |

## Staged Rollout

Do not deploy to all users at once.

```
Stage 1: Internal testing (team only)
Stage 2: Canary (5% of traffic)
Stage 3: Gradual rollout (25% -> 50% -> 100%)
Stage 4: Monitor for 48 hours before declaring stable
```

At each stage, check:
- Error rate is within acceptable bounds
- Task completion rate has not dropped
- Cost per task has not spiked
- No new guardrail violations

## See Also

- `hub/deployment.md` — Deployment overview
- `research/deployment/monitoring-observability.md` — What to monitor
- `research/deployment/cost-optimization.md` — Cost management
