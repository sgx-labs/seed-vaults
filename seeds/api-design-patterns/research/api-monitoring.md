---
title: "API Monitoring and Observability"
tags: [monitoring, observability, metrics, logging, tracing, alerting, sla, latency]
content_type: research
domain: api-design
---

# API Monitoring and Observability

## The Core Idea

You can't fix what you can't see. API monitoring tells you when things are broken. Observability tells you why. The three pillars: metrics (numbers), logs (events), and traces (request flows).

## The Three Pillars

### Metrics
Numerical measurements aggregated over time.

**Key API metrics**:

| Metric | What It Tells You | Alert Threshold |
|--------|-------------------|-----------------|
| Request rate (RPS) | Traffic volume | Sudden drops or spikes |
| Error rate (5xx %) | Server health | > 1% of requests |
| Latency (p50, p95, p99) | Response speed | p99 > 2x normal |
| Availability (uptime %) | Service reliability | < 99.9% |
| Saturation | Resource usage (CPU, memory, connections) | > 80% |

**USE method** (for infrastructure): Utilization, Saturation, Errors.
**RED method** (for services): Rate, Errors, Duration.

### Logs
Structured event records. Every request should produce a log entry.

```json
{
  "timestamp": "2025-01-15T10:30:00Z",
  "level": "info",
  "method": "POST",
  "path": "/api/orders",
  "status": 201,
  "duration_ms": 45,
  "user_id": "usr_123",
  "request_id": "req_abc",
  "trace_id": "trace_xyz"
}
```

**Best practices**:
- Structured JSON (not plain text)
- Include request_id on every log line
- Include trace_id for distributed tracing correlation
- Log request and response metadata, not full bodies (PII risk)
- Log at appropriate levels (error for 5xx, warn for 4xx, info for 2xx)

### Traces
Follow a request through multiple services.

```
[trace_id: abc]
  order-service: POST /orders (45ms)
    -> payment-service: POST /charge (20ms)
    -> inventory-service: POST /reserve (15ms)
    -> notification-service: POST /send (5ms)
```

**Tools**: OpenTelemetry, Jaeger, Zipkin, Datadog APM.

## What to Monitor

### Endpoint-Level Metrics
```
Per endpoint:
  - Request count
  - Error count (4xx and 5xx separately)
  - Latency distribution (p50, p95, p99)
  - Request size
  - Response size
```

### Business Metrics
```
  - Orders per minute
  - Sign-ups per hour
  - API key creation rate
  - Webhook delivery success rate
```

### Infrastructure Metrics
```
  - CPU and memory usage
  - Database connection pool utilization
  - Queue depth
  - Cache hit rate
```

## Alerting Strategy

### Alert on Symptoms, Not Causes

```
Good: "Error rate on /api/payments exceeded 5% for 5 minutes"
Bad:  "CPU usage on server-3 exceeded 80%"
```

CPU might be high because of legitimate traffic. Error rate tells you users are affected.

### Severity Levels

| Severity | Criteria | Response |
|----------|----------|----------|
| P1 Critical | Service down, data loss risk | Wake someone up |
| P2 High | Significant degradation, high error rate | Respond within 30 min |
| P3 Medium | Elevated errors, slow responses | Respond within 4 hours |
| P4 Low | Anomaly, non-impacting | Review next business day |

### Avoid Alert Fatigue
- Only alert on actionable conditions
- Use thresholds, not absolutes (5% error rate, not "any error")
- Require duration (sustained for 5 minutes, not a single spike)
- Group related alerts

## Health Check Endpoints

```
GET /health          # Simple alive check
  200 OK {"status": "healthy"}

GET /health/ready    # Ready to serve traffic
  200 OK {"status": "ready", "checks": {"database": "ok", "cache": "ok"}}
  503 Service Unavailable {"status": "not ready", "checks": {"database": "failing"}}
```

- `/health` for load balancer checks (is the process running?)
- `/health/ready` for readiness checks (can it serve traffic?)
- Don't include sensitive data in health check responses

## See Also

- `research/load-testing.md` -- Testing performance before monitoring it
- `hub/error-handling.md` -- Error responses that are monitorable
- `research/circuit-breaker.md` -- Automated response to failures
- `hub/api-gateway.md` -- Centralized monitoring at the gateway
