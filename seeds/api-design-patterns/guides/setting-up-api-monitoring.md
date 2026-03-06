---
title: "Setting Up API Monitoring"
tags: [guide, monitoring, metrics, alerting, health-check, observability]
content_type: guide
domain: api-design
---

# Setting Up API Monitoring

## Overview

This guide walks through setting up monitoring for an API, from basic health checks to full observability. Start simple and add layers as your API grows.

## Step 1: Health Check Endpoint

The most basic monitoring. Create two endpoints:

```
GET /health
  -> 200 {"status": "healthy", "timestamp": "2025-01-15T10:30:00Z"}

GET /health/ready
  -> 200 {
    "status": "ready",
    "checks": {
      "database": {"status": "ok", "latency_ms": 3},
      "cache": {"status": "ok", "latency_ms": 1}
    }
  }

  -> 503 {
    "status": "not_ready",
    "checks": {
      "database": {"status": "failing", "error": "connection refused"},
      "cache": {"status": "ok", "latency_ms": 1}
    }
  }
```

- `/health` -- is the process running? (for load balancers)
- `/health/ready` -- can it serve traffic? (for readiness probes)

Do NOT include auth on health check endpoints. Monitoring tools need unauthenticated access.

## Step 2: Request Logging

Add structured logging middleware that captures every request:

```
function requestLogger(request, response, next):
  startTime = now()
  requestId = generateUUID()

  request.headers["X-Request-Id"] = requestId
  response.setHeader("X-Request-Id", requestId)

  next()

  log({
    timestamp: now(),
    request_id: requestId,
    method: request.method,
    path: request.path,
    status: response.statusCode,
    duration_ms: now() - startTime,
    user_id: request.user?.id,
    ip: request.ip,
    user_agent: request.headers["user-agent"]
  })
```

Rules:
- Log in JSON format (machine parseable)
- Include request_id on every log line
- Log duration for performance tracking
- Never log request/response bodies (PII risk)
- Never log auth tokens or passwords

## Step 3: Metrics Collection

Track these metrics for every endpoint:

```
Per endpoint:
  - request_count{method, path, status}    # Total requests
  - request_duration{method, path}         # Latency histogram
  - error_count{method, path, status}      # 4xx and 5xx separately

Global:
  - active_connections                      # Current open connections
  - database_pool_size                      # DB connections in use
  - database_query_duration                 # Query latency
```

Expose metrics at `/metrics` in Prometheus format, or push to your metrics service.

## Step 4: Alerting

Start with these four alerts:

### Alert 1: High Error Rate
```
Condition: 5xx error rate > 1% for 5 minutes
Severity: P2 (respond within 30 minutes)
```

### Alert 2: High Latency
```
Condition: p99 latency > 3 seconds for 5 minutes
Severity: P3 (respond within 4 hours)
```

### Alert 3: Service Down
```
Condition: /health returns non-200 for 2 minutes
Severity: P1 (wake someone up)
```

### Alert 4: Error Spike
```
Condition: Error rate increases by 5x compared to same time yesterday
Severity: P2
```

### Alert Rules
- Require sustained conditions (5 minutes, not instant)
- Alert on symptoms (error rate) not causes (CPU)
- Keep it actionable -- if you can't act on it, don't alert on it

## Step 5: Dashboard

Build a dashboard with four panels:

```
Panel 1: Request Rate (requests per second, by endpoint)
Panel 2: Error Rate (% of 4xx and 5xx, by endpoint)
Panel 3: Latency (p50, p95, p99, by endpoint)
Panel 4: Saturation (CPU, memory, DB connections)
```

This is the RED method (Rate, Errors, Duration) plus saturation.

## Step 6: Distributed Tracing (When Ready)

Once you have multiple services, add tracing:

1. Generate a trace_id at the API gateway or first service
2. Pass trace_id in headers between services
3. Each service logs trace_id with every request
4. View the full request flow in Jaeger/Zipkin/Datadog

Start with logging trace_id. Add a tracing service when you need the visualization.

## See Also

- `research/api-monitoring.md` -- Monitoring and observability deep dive
- `research/load-testing.md` -- Test what you're monitoring
- `hub/error-handling.md` -- Error responses that are easy to monitor
