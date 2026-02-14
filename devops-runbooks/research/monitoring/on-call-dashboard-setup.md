---
title: "On-Call Dashboard Setup"
tags: [on-call, dashboard, monitoring, golden-signals, setup]
content_type: research
domain: operations
---

# On-Call Dashboard Setup

## The Question

What dashboard should the on-call engineer look at first?

## The On-Call Dashboard

This is the single dashboard every on-call engineer opens at the start of their shift and checks when paged. It answers one question: **Is everything healthy right now?**

## Layout

```
┌─────────────────────────────────────────────────────────────┐
│  ON-CALL OVERVIEW            Last updated: auto-refresh 30s │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  ACTIVE ALERTS: 0 critical | 1 warning                     │
│  [List of active alerts with links to runbooks]             │
│                                                             │
├──────────────────────┬──────────────────────────────────────┤
│  OVERALL ERROR RATE  │  OVERALL p99 LATENCY                │
│  ▁▁▁▁▁▁▁▁▁ 0.03%    │  ▁▁▁▁▁▁▁▁▁ 210ms                   │
│  (target: <0.1%)     │  (target: <500ms)                   │
├──────────────────────┼──────────────────────────────────────┤
│  SERVICE HEALTH      │  RECENT DEPLOYS                     │
│  API Gateway:    OK  │  api-server v2.15.0   (2h ago)      │
│  Auth Service:   OK  │  payment-svc v1.8.3   (1d ago)      │
│  Payment API:    OK  │                                      │
│  Search Service: OK  │  UPCOMING MAINTENANCE               │
│  Database:       OK  │  DB upgrade window: Sat 02:00 UTC   │
│  Cache:          OK  │                                      │
├──────────────────────┴──────────────────────────────────────┤
│  INFRASTRUCTURE                                             │
│  Nodes: 8/8 healthy | CPU avg: 45% | Memory avg: 62%      │
│  Disk: primary 67% | replica 52%                           │
│  DB connections: 42/200 (21%)                              │
└─────────────────────────────────────────────────────────────┘
```

## What To Include

### Must-have panels

1. **Active alerts** — Current firing alerts with severity and runbook links
2. **Overall error rate** — Aggregated across all services, with SLO target line
3. **Overall p99 latency** — Aggregated, with SLO target line
4. **Service health grid** — Green/yellow/red for each critical service
5. **Recent deploys** — Last 24 hours, with who and what
6. **Infrastructure status** — Node health, CPU, memory, disk

### Nice-to-have panels

7. **Error budget remaining** — How much downtime budget is left this month
8. **Request rate** — Is traffic normal or spiking?
9. **Queue depths** — Are message queues backing up?
10. **External dependency status** — Third-party API health

## Datadog Example Queries

```
# Overall error rate (all services)
sum:trace.http.request.errors{env:production}.as_rate() /
sum:trace.http.request.hits{env:production}.as_rate() * 100

# Overall p99 latency
p99:trace.http.request.duration{env:production}

# Service health (per service)
# Create a monitor summary widget grouped by service

# Node CPU average
avg:system.cpu.user{env:production} by {host}

# Database connections
avg:postgresql.connections{env:production}
```

## On-Call Shift Start Checklist

When starting your on-call shift:

1. [ ] Open the on-call dashboard
2. [ ] Check active alerts — any ongoing issues?
3. [ ] Read the on-call handoff from previous shift
4. [ ] Check recent deploys — anything that might cause issues?
5. [ ] Verify your PagerDuty notifications work (test page)
6. [ ] Check upcoming maintenance windows

## Related

- `research/monitoring/dashboard-design.md` — Dashboard design principles
- `research/monitoring/alert-fatigue.md` — Alert configuration
- `research/incidents/oncall-rotation.md` — On-call best practices
- `entities/datadog.md` — Datadog dashboard setup
- `entities/pagerduty.md` — PagerDuty integration
