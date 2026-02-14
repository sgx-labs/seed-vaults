---
title: "How Do I Monitor Agent Behavior in Production?"
tags: [monitoring, observability, logging, tracing, metrics, production]
content_type: research
domain: engineering
---

# How Do I Monitor Agent Behavior in Production?

## The Question

What should you monitor, log, and trace in a production agent system?

## The Three Pillars for Agents

### 1. Metrics (What happened, aggregated)

```
- Token usage per session (input, output, total)
- Cost per session / per user / per task type
- Tool call count and success rate
- Task completion rate
- Latency distribution (p50, p95, p99)
- Guardrail trigger rate
- Error rate by type
- Sessions per hour/day
```

### 2. Logs (What happened, in detail)

```json
{
  "timestamp": "2026-02-14T10:30:00Z",
  "session_id": "sess_abc123",
  "event": "tool_call",
  "tool": "search_code",
  "params": {"query": "auth middleware"},
  "result_status": "success",
  "result_count": 3,
  "latency_ms": 450,
  "tokens_used": 1200
}
```

Log every agent action with enough context to reconstruct what happened. Include session ID for correlation.

### 3. Traces (What happened, in sequence)

A trace follows one task from start to finish:

```
Trace: task_456
  |-- Agent receives task (t=0ms)
  |-- Agent calls search_code (t=100ms, 450ms)
  |-- Agent calls read_file (t=600ms, 200ms)
  |-- Agent calls write_file (t=1200ms, 150ms)
  |-- Agent returns result (t=1800ms)
  Total: 1800ms, 4 LLM calls, 3 tool calls, 8400 tokens
```

## What to Alert On

| Condition | Severity | Action |
|-----------|----------|--------|
| Error rate > 5% for 5 minutes | Critical | Page on-call |
| Cost per session > 3x average | High | Investigate, may need cost cap |
| Task completion rate < 80% | High | Check for model/tool regression |
| p95 latency > 30s | Medium | Check for slow tools or rate limits |
| Guardrail trigger spike | High | Potential attack or prompt issue |
| New error type appears | Medium | Investigate and categorize |

## Observability Tools

| Tool | Type | Agent-specific features |
|------|------|----------------------|
| Braintrust | Agent observability | LLM tracing, quality scoring, prompt management |
| LangSmith | Agent observability | LangGraph integration, trace visualization |
| Helicone | LLM proxy | Token tracking, cost monitoring, caching |
| OpenTelemetry | General observability | Custom spans for agent actions |
| Datadog | General monitoring | Custom metrics, dashboards, alerting |

## Debugging with Traces

When an agent fails, the trace tells you exactly what went wrong:

```
Trace: task_789 (FAILED)
  |-- Agent receives task (t=0ms)
  |-- Agent calls search_code (t=100ms, SUCCESS)
  |-- Agent calls read_file (t=600ms, SUCCESS)
  |-- Agent calls write_file (t=1200ms, ERROR: permission denied)
  |-- Agent retries write_file (t=1500ms, ERROR: permission denied)
  |-- Agent returns error (t=2000ms)
  Root cause: Agent tried to write outside project directory
```

Without traces, you would only see "task failed." With traces, you see exactly where and why.

## The Token Dashboard

Track token usage over time to catch cost creep:

```
Daily Token Report:
  Total sessions: 1,247
  Total tokens: 45.2M (input: 38.1M, output: 7.1M)
  Avg per session: 36,247 tokens
  Cost: $127.50
  Most expensive task type: code_review ($2.30 avg)
  Cheapest task type: status_check ($0.12 avg)
```

## See Also

- `hub/deployment.md` — Deployment overview
- `research/deployment/agent-debugging.md` — Debugging agents
- `research/deployment/cost-optimization.md` — Cost management
