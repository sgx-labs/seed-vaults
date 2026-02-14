---
title: "Dashboard Design for Operations"
tags: [dashboards, monitoring, visualization, golden-signals, SRE]
content_type: research
domain: operations
---

# Dashboard Design for Operations

## The Question

How do I design dashboards that help during incidents?

## Principle: Dashboards answer questions, not display data

A good dashboard answers: "Is the service healthy? If not, where is the problem?" A bad dashboard shows 30 charts and requires a PhD to interpret.

## Dashboard Hierarchy

### Level 1: Service Overview (the "on-call" dashboard)

One dashboard per critical service. Shows the four golden signals at a glance.

```
┌─────────────────────────────────────────────────┐
│  Service: Payment API          Status: HEALTHY  │
├────────────────┬────────────────────────────────┤
│  Requests/sec  │  Error Rate                    │
│  ████████ 1.2K │  ▁▁▁▁▁▁▁▁ 0.02%              │
├────────────────┼────────────────────────────────┤
│  p99 Latency   │  CPU / Memory                  │
│  ████▁▁▁ 180ms │  ███▁▁ 62%  /  ████▁ 71%     │
├────────────────┴────────────────────────────────┤
│  Recent deploys: v2.14.3 (2h ago)               │
│  Active alerts: None                            │
└─────────────────────────────────────────────────┘
```

**Content:**
- Request rate (traffic)
- Error rate (errors) with target line at SLO threshold
- p50, p95, p99 latency (latency) with target line
- CPU and memory utilization (saturation)
- Recent deploys overlaid on metrics
- Active alerts panel

### Level 2: System Dashboard

One per infrastructure layer (database, cache, message queue).

**Database dashboard:**
- Active connections vs. max
- Query latency (p50, p99)
- Replication lag
- Disk usage and IOPS
- Slow query count

**Cache dashboard:**
- Hit rate (target: >95%)
- Memory usage
- Eviction rate
- Connection count

### Level 3: Business Dashboard

Combines technical and business metrics for leadership.

- Revenue per minute (overlaid with error rate)
- Signups per hour
- Active users
- Failed transactions

## Dashboard Anti-Patterns

| Anti-Pattern | Problem | Fix |
|-------------|---------|-----|
| Wall of numbers | Cannot spot anomalies | Use time-series graphs |
| Too many charts | Information overload | Max 8-10 widgets per dashboard |
| No baselines | Cannot tell if values are normal | Add SLO target lines |
| Missing deploy markers | Cannot correlate deploys with changes | Overlay deploy events |
| Stale dashboards | Show metrics for services that changed | Review dashboards quarterly |

## Dashboard Naming Convention

```
[Team] - [Service] - [Level]

Examples:
  Platform - Payment API - Overview
  Platform - Payment API - Database
  Platform - Payment API - Business
  Infra - Kubernetes Cluster - Overview
  Infra - Network - Overview
```

## Related

- `research/monitoring/slo-sli-guide.md` — SLO target lines on dashboards
- `research/monitoring/alert-fatigue.md` — Alert context in dashboards
- `entities/datadog.md` — Datadog dashboard setup
- `hub/monitoring.md` — Monitoring overview
