---
title: "Datadog — Monitoring Setup, Dashboards, APM"
tags: [datadog, monitoring, dashboards, APM, entity]
content_type: entity
domain: operations
---

# Datadog — Current State

## What It Is

Cloud monitoring and analytics platform. Collects metrics, logs, and traces from your infrastructure and applications. Provides dashboards, alerting, and APM (Application Performance Monitoring).

## Key Features

| Feature | Use Case |
|---------|----------|
| **Infrastructure** | Host metrics, container monitoring, cloud integrations |
| **APM** | Distributed tracing, service maps, latency analysis |
| **Logs** | Log aggregation, parsing, correlation with metrics |
| **Monitors** | Alerting on metrics, logs, and composite conditions |
| **Dashboards** | Visualization of system health and business metrics |
| **Synthetics** | Synthetic monitoring (API tests, browser tests) |

## Agent Installation

```bash
# Linux (one-liner)
DD_AGENT_MAJOR_VERSION=7 DD_API_KEY=YOUR_API_KEY \
  bash -c "$(curl -L https://install.datadoghq.com/scripts/install_script.sh)"

# Docker
docker run -d --name datadog-agent \
  -e DD_API_KEY=YOUR_API_KEY \
  -e DD_SITE="datadoghq.com" \
  -v /var/run/docker.sock:/var/run/docker.sock:ro \
  -v /proc/:/host/proc/:ro \
  -v /sys/fs/cgroup/:/host/sys/fs/cgroup:ro \
  datadog/agent:latest

# Kubernetes (Helm)
helm install datadog-agent datadog/datadog \
  --set datadog.apiKey=YOUR_API_KEY \
  --set datadog.site="datadoghq.com"
```

## Creating Monitors (Alerts)

```bash
# Via API: Create a metric monitor
curl -X POST "https://api.datadoghq.com/api/v1/monitor" \
  -H "DD-API-KEY: YOUR_API_KEY" \
  -H "DD-APPLICATION-KEY: YOUR_APP_KEY" \
  -d '{
    "type": "metric alert",
    "query": "avg(last_5m):avg:system.cpu.user{service:api} > 90",
    "name": "High CPU on API Service",
    "message": "CPU > 90% for 5 minutes. Check runbook: research/runbooks/high-cpu-memory-disk.md @pagerduty-api-team",
    "priority": 1,
    "options": {
      "thresholds": {"critical": 90, "warning": 70},
      "notify_no_data": true,
      "renotify_interval": 300
    }
  }'
```

## Useful Queries

```
# Error rate by service
sum:trace.http.request.errors{env:production} by {service}.as_rate()

# p99 latency
p99:trace.http.request.duration{env:production} by {service}

# Disk usage by host
avg:system.disk.in_use{*} by {host} * 100

# Container OOM kills
sum:docker.mem.failed_cnt{*} by {container_name}
```

## Dashboard Best Practices

1. **Service overview** — One dashboard per service with golden signals
2. **On-call dashboard** — Aggregated health of all critical services
3. **Deploy dashboard** — Error rates and latency overlaid with deploy markers
4. **Business metrics** — Revenue, signups, active users alongside infra metrics

## Related

- `research/monitoring/dashboard-design.md` — Dashboard design patterns
- `research/monitoring/alert-fatigue.md` — Alert tuning to reduce noise
- `entities/pagerduty.md` — Routing Datadog alerts to PagerDuty

## Last Updated

February 2026
