---
title: "Monitoring — Alerting Strategy, Dashboards, SLOs"
tags: [monitoring, alerting, dashboards, SLO, SLI, observability, golden-signals, hub]
content_type: hub
domain: operations
---

# Monitoring

## Overview

Monitoring encompasses alerting, SLO/SLI measurement, dashboards, log aggregation, synthetic health checks, and on-call observability. This hub covers how to define service level objectives, set alert thresholds that avoid alert fatigue, design actionable dashboards using golden signals (latency, traffic, errors, saturation), ship structured logs, and configure synthetic monitoring probes. Effective monitoring reduces mean time to detect (MTTD), surfaces problems before users notice, and drives error budget decisions.

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

## See Also

- `hub/incident-response.md` — Monitoring triggers incident response workflows
- `hub/runbooks.md` — Runbooks executed when monitoring detects an issue
- `hub/post-mortems.md` — Post-mortems improve monitoring based on lessons learned
- `hub/infrastructure.md` — Infrastructure health feeds monitoring dashboards
