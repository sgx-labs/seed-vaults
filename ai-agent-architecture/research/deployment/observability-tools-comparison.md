---
title: "Which Observability Tools Work Best for Agent Systems?"
tags: [observability, tools, monitoring, tracing, comparison, Braintrust, LangSmith, Helicone, OpenTelemetry]
content_type: research
domain: engineering
---

# Which Observability Tools Work Best for Agent Systems?

## The Question

What tools should I use to monitor, trace, and debug agents in production?

## The Observability Stack

Agent observability needs three layers:

```
LLM-Specific Observability   — Token usage, model calls, prompt traces
Agent-Specific Observability  — Tool calls, state transitions, task completion
Infrastructure Observability  — Latency, errors, compute, cost
```

General-purpose tools (Datadog, Grafana) handle layer 3. Agent-specific tools handle layers 1-2. You typically need both.

## Agent Observability Tools (2026)

| Tool | Focus | Pricing | Key Feature |
|------|-------|---------|-------------|
| Braintrust | Quality + traces | Free tier + usage | Online scoring, prompt management |
| LangSmith | LangGraph traces | Free tier + usage | Deep LangGraph integration |
| Helicone | LLM proxy | Free tier + usage | Transparent proxy, caching |
| Arize Phoenix | ML observability | Open source | Embedding analysis, drift detection |
| Portkey | LLM gateway | Free tier + usage | Multi-provider routing + observability |

## What to Look For

### Must-Have Features

- **Trace visualization** — See the full sequence of agent decisions and tool calls
- **Token accounting** — Track input/output tokens per call and per session
- **Cost tracking** — Real-time spend per model, per task type, per user
- **Latency breakdown** — Where time is spent (model inference vs tool execution)
- **Error correlation** — Link errors to specific agent decisions

### Nice-to-Have Features

- **Prompt versioning** — Track which prompt version produced which results
- **Quality scoring** — Automated assessment of agent outputs
- **Replay** — Re-run a session from any point with different parameters
- **Alerting** — Notify when metrics cross thresholds
- **Dashboard** — Real-time overview of agent fleet health

## DIY Observability

If you want to avoid vendor lock-in, build on open standards:

```python
from opentelemetry import trace
from opentelemetry.sdk.trace import TracerProvider

tracer = trace.get_tracer("agent-service")

def agent_step(messages, tools):
    with tracer.start_as_current_span("agent.step") as span:
        span.set_attribute("message_count", len(messages))
        span.set_attribute("tool_count", len(tools))

        response = llm.generate(messages, tools=tools)

        span.set_attribute("output_tokens", response.usage.output_tokens)
        span.set_attribute("stop_reason", response.stop_reason)
        span.set_attribute("tool_calls", len(response.tool_calls))

        return response
```

Export to any OpenTelemetry-compatible backend (Jaeger, Grafana Tempo, Datadog).

## Cost of No Observability

Without observability, you will:
- Discover cost spikes from invoices, not dashboards
- Debug agent failures by reading raw logs
- Miss quality regressions until users complain
- Have no data for optimizing agent performance

Observability is not optional for production agents. Budget for it from day one.

## See Also

- `research/deployment/monitoring-observability.md` — What to monitor
- `research/deployment/agent-debugging.md` — Debugging agents
- `hub/deployment.md` — Deployment overview
