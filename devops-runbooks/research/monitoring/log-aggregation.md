---
title: "Log Aggregation Strategy"
tags: [logging, aggregation, ELK, structured-logs, observability]
content_type: research
domain: operations
---

# Log Aggregation Strategy

## The Question

How do I set up log aggregation so I can find what I need during an incident?

## Principle: Structured logs or nothing

Unstructured logs are grep-able. Structured logs are queryable, filterable, and aggregatable. The difference matters at 3am when you need to find one error across 50 services.

## Step 1: Use structured logging (JSON)

```json
{
  "timestamp": "2026-02-14T03:42:15Z",
  "level": "error",
  "service": "payment-api",
  "method": "POST",
  "path": "/v1/charges",
  "status": 500,
  "duration_ms": 2340,
  "request_id": "req-abc123",
  "user_id": "usr-456",
  "error": "connection refused: payment-gateway:443",
  "trace_id": "trace-789"
}
```

**Required fields for every log line:**
- `timestamp` (ISO 8601 UTC)
- `level` (debug, info, warn, error, fatal)
- `service` (which service emitted this)
- `request_id` (correlate related logs)
- `trace_id` (connect to distributed traces)

## Step 2: Ship logs to a central store

### Kubernetes: Use a DaemonSet log collector

```bash
# Install Fluent Bit (lightweight log shipper)
helm install fluent-bit fluent/fluent-bit \
  --set config.outputs.elasticsearch.host=elasticsearch.example.com \
  --set config.outputs.elasticsearch.port=9200

# Or ship to Datadog
helm install fluent-bit fluent/fluent-bit \
  --set config.outputs.datadog.apiKey=YOUR_API_KEY
```

### Direct application logging

```bash
# Application writes to stdout (K8s collects automatically)
# Just ensure your app logs JSON to stdout/stderr

# Docker: logs are at
/var/lib/docker/containers/<id>/<id>-json.log

# Kubernetes: logs are at
/var/log/pods/<namespace>_<pod>_<uid>/<container>/0.log
```

## Step 3: Query logs during incidents

```bash
# Find errors for a specific service in the last hour
# Elasticsearch/Kibana query:
# service:"payment-api" AND level:"error" AND @timestamp:[now-1h TO now]

# Datadog log query:
# service:payment-api status:error

# Find all logs for a specific request
# request_id:"req-abc123"

# Find error patterns (group by error message)
# service:"payment-api" AND level:"error" | stats count by error
```

## Log Retention Policy

| Log Level | Retention | Storage |
|-----------|-----------|---------|
| Error/Fatal | 90 days | Hot storage |
| Warn | 30 days | Hot storage |
| Info | 14 days | Hot storage |
| Debug | 3 days (prod) | Hot storage |
| All levels | 1 year | Cold/archive |

## Log Level Guidelines

| Level | When to use | Example |
|-------|------------|---------|
| **fatal** | Process cannot continue | "Database connection string missing, cannot start" |
| **error** | Operation failed, needs attention | "Payment charge failed: gateway timeout" |
| **warn** | Something unexpected but handled | "Retry 2/3 for downstream call" |
| **info** | Normal operations | "Server started on port 8080" |
| **debug** | Diagnostic detail | "Query returned 42 rows in 3ms" |

## Anti-Patterns

1. **Logging sensitive data.** Never log passwords, tokens, credit card numbers, or PII.
2. **Logging at wrong level.** Expected 404s are not errors. Retries with success are not errors.
3. **No request ID.** Without correlation IDs, you cannot trace a request across services.
4. **Huge log volume.** Debug logging in production at high traffic will fill your storage and your budget.

## Related

- `research/monitoring/dashboard-design.md` — Log panels in dashboards
- `research/monitoring/slo-sli-guide.md` — Error logs feed SLI calculations
- `entities/datadog.md` — Datadog log management
- `hub/monitoring.md` — Monitoring overview
