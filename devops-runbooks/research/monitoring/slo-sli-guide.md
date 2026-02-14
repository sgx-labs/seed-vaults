---
title: "SLO and SLI Definition Guide"
tags: [SLO, SLI, SLA, error-budget, reliability, monitoring]
content_type: research
domain: operations
---

# SLO and SLI Definition Guide

## The Question

What SLOs should I define for my service and how do I measure them?

## Terminology

| Term | Definition | Example |
|------|-----------|---------|
| **SLI** (Service Level Indicator) | A metric that measures service quality | 99.2% of requests return 2xx in <500ms |
| **SLO** (Service Level Objective) | Internal target for an SLI | "99.9% of requests succeed" |
| **SLA** (Service Level Agreement) | Contract with customers, with penalties | "99.95% uptime or credits issued" |
| **Error Budget** | Allowed failures before SLO is breached | 0.1% = 43.2 minutes downtime/month |

## Step 1: Define your SLIs

Start with the four golden signals. Pick the ones that matter most to your users.

### Availability SLI

```
Availability = (successful requests) / (total requests) * 100

# Prometheus query:
sum(rate(http_requests_total{status!~"5.."}[5m])) /
sum(rate(http_requests_total[5m]))
```

### Latency SLI

```
Latency = % of requests faster than threshold

# Prometheus query (% under 500ms):
sum(rate(http_request_duration_seconds_bucket{le="0.5"}[5m])) /
sum(rate(http_request_duration_seconds_count[5m]))
```

### Error Rate SLI

```
Error Rate = (5xx responses) / (total responses) * 100

# Prometheus query:
sum(rate(http_requests_total{status=~"5.."}[5m])) /
sum(rate(http_requests_total[5m]))
```

## Step 2: Set SLO targets

| Service Tier | Availability | Latency (p99) | Error Rate | Monthly Downtime |
|-------------|--------------|---------------|------------|-----------------|
| **Tier 1 (Critical)** | 99.9% | <500ms | <0.1% | 43.2 minutes |
| **Tier 2 (Important)** | 99.5% | <1s | <0.5% | 3.6 hours |
| **Tier 3 (Standard)** | 99.0% | <2s | <1% | 7.2 hours |

### Common SLO examples

```
Payment API:      99.99% availability, p99 < 300ms
User API:         99.9% availability, p99 < 500ms
Search API:       99.5% availability, p99 < 1s
Admin Dashboard:  99.0% availability, p99 < 2s
Batch Processing: 99.0% completion rate within 1 hour
```

## Step 3: Calculate error budget

```
Error Budget = 100% - SLO target

For 99.9% monthly SLO:
  Error budget = 0.1%
  Monthly minutes = 43,200 (30 days)
  Allowed downtime = 43,200 * 0.001 = 43.2 minutes

For 99.99% monthly SLO:
  Error budget = 0.01%
  Allowed downtime = 43,200 * 0.0001 = 4.32 minutes
```

## Step 4: Use error budgets for decisions

| Budget Remaining | Action |
|-----------------|--------|
| >50% | Normal development velocity. Ship features. |
| 25-50% | Proceed with caution. Extra testing for risky changes. |
| 10-25% | Slow down. Focus on reliability improvements. |
| <10% | Feature freeze. All engineering on reliability. |
| 0% (exhausted) | Full stop. Only reliability work until budget recovers. |

## Step 5: Monitor SLO burn rate

```
# Prometheus: 1-hour burn rate (how fast you're consuming budget)
1 - (
  sum(rate(http_requests_total{status!~"5.."}[1h])) /
  sum(rate(http_requests_total[1h]))
) / (1 - 0.999)  # Replace 0.999 with your SLO

# Alert when burning budget too fast:
# If burn_rate > 14.4 for 2 min → page (1% budget consumed in 1 hour)
# If burn_rate > 6 for 15 min → page (5% budget consumed in 6 hours)
# If burn_rate > 1 for 1 hour → ticket (budget being consumed at normal rate)
```

## Common Mistakes

1. **Setting SLOs too high.** 99.999% means 26 seconds of downtime per month. You cannot maintain that.
2. **Measuring the wrong thing.** SLIs must reflect user experience, not internal metrics.
3. **SLO without error budget policy.** An SLO without consequences is just a dashboard.
4. **Same SLO for everything.** Your admin panel does not need the same SLO as your payment API.

## Related

- `research/monitoring/alert-fatigue.md` — Alert based on SLO burn rate
- `research/monitoring/dashboard-design.md` — Displaying SLO status
- `research/incidents/incident-metrics.md` — MTTR and incident tracking
- `hub/monitoring.md` — Monitoring overview
