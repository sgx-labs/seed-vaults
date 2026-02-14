---
title: "Monitoring — Alerting Strategy, Dashboards, SLOs"
tags: [monitoring, alerting, dashboards, SLO, SLI, hub]
content_type: hub
domain: operations
---

# Monitoring

## Overview

Monitoring is not dashboards. Monitoring is the system that wakes you up when something is broken and gives you enough information to fix it fast. Good monitoring catches problems before users do. Bad monitoring wakes you up for nothing and trains you to ignore alerts.

## The Three Pillars

1. **Metrics** — Numeric measurements over time (CPU, latency, error rate)
2. **Logs** — Discrete events with context (error messages, request details)
3. **Traces** — End-to-end request paths across services (distributed tracing)

## Key Research Notes

| Topic | Note |
|-------|------|
| SLO/SLI definition guide | `research/monitoring/slo-sli-guide.md` |
| Alerting without alert fatigue | `research/monitoring/alert-fatigue.md` |
| Synthetic monitoring setup | `research/monitoring/synthetic-monitoring.md` |
| Dashboard design | `research/monitoring/dashboard-design.md` |
| Log aggregation | `research/monitoring/log-aggregation.md` |

## Alert Severity Mapping

| Alert Level | Action Required | Example |
|------------|-----------------|---------|
| **Critical** | Page immediately, wake someone up | Error rate >5%, total outage |
| **Warning** | Investigate within 1 hour | Disk >80%, latency p99 >2s |
| **Info** | Review next business day | Deploy completed, scaling event |

## The Golden Signals (Google SRE)

Monitor these four for every service:

1. **Latency** — Time to serve a request (separate success vs. error latency)
2. **Traffic** — Requests per second
3. **Errors** — Rate of failed requests (5xx, timeouts)
4. **Saturation** — How full your service is (CPU, memory, queue depth)

## Entity References

- `entities/datadog.md` — Datadog setup, dashboards, APM
- `entities/pagerduty.md` — PagerDuty integration, escalation policies
