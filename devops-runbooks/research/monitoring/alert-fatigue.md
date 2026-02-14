---
title: "Alerting Without Alert Fatigue"
tags: [alerting, alert-fatigue, noise, on-call, monitoring]
content_type: research
domain: operations
---

# Alerting Without Alert Fatigue

## The Question

How do I set up effective alerting without alert fatigue?

## The Problem

Alert fatigue is the number one killer of on-call effectiveness. When every alert is urgent, none are urgent. Engineers start ignoring pages, and when the real outage comes, nobody responds.

## Rule 1: Every alert must be actionable

If receiving an alert does not require a human to do something right now, it should not be an alert. It should be a dashboard, a log, or a ticket.

**Good alerts:**
- "Error rate for payment API >5% for 5 minutes" (ACTION: investigate, possibly rollback)
- "Disk usage on db-primary >90%" (ACTION: clean up or expand)
- "Zero successful health checks for 2 minutes" (ACTION: check service, restart)

**Bad alerts (convert to dashboards):**
- "CPU at 75%" (what am I supposed to do about it?)
- "Deploy completed" (great, this is informational)
- "Single request returned 500" (one error is not an incident)

## Rule 2: Alert on symptoms, not causes

```
WRONG: Alert when CPU > 80%
RIGHT: Alert when p99 latency > 2 seconds

WRONG: Alert when memory > 75%
RIGHT: Alert when error rate > 1%

WRONG: Alert when connection pool > 50%
RIGHT: Alert when users are getting timeout errors
```

**Exception:** Disk space. Alert on disk > 85% because by the time users notice, it is too late.

## Rule 3: Set proper thresholds

```yaml
# Example Datadog monitor configuration pattern

# WARNING: Things to look at soon
warning_threshold: 70   # CPU
warning_duration: "15m"  # Sustained, not a spike

# CRITICAL: Things to fix now
critical_threshold: 90   # CPU
critical_duration: "5m"  # Shorter duration for critical

# Use rate-of-change alerts for gradual degradation
# "Disk growing by >1GB/hour" catches issues before they hit 90%
```

## Rule 4: Use severity levels properly

| Severity | Action | Notification | Example |
|----------|--------|-------------|---------|
| **Critical** | Wake someone up | PagerDuty page | Service down, data loss |
| **High** | Respond within 15 min | PagerDuty + Slack | Error rate >5% |
| **Warning** | Respond within 1 hour | Slack only | Disk >80%, latency creeping |
| **Info** | Review next day | Dashboard only | Deploy complete, scaling event |

## Rule 5: Implement alert grouping and deduplication

```bash
# PagerDuty: Group related alerts into one incident
# Set alert_grouping in service settings:
# - Intelligent grouping (ML-based)
# - Time-based grouping (within 5 minutes)
# - Content-based grouping (same service)

# Datadog: Use composite monitors
# "Alert only if BOTH conditions are true"
# Example: High latency AND high error rate
```

## Alert Audit Process (Monthly)

Every month, review the last 30 days of alerts:

```
For each alert that fired:
1. Was it actionable? (Did someone need to do something?)
2. Was it timely? (Did it fire early enough to matter?)
3. Was it clear? (Did the on-call know what to do?)
4. Was it real? (Was there actual user impact?)
```

| Category | Action |
|----------|--------|
| Actionable + real impact | Keep as-is |
| Actionable but too noisy | Adjust threshold or add dedup |
| Not actionable | Downgrade to dashboard/log |
| False positive | Fix detection logic or delete |

**Target: >70% of alerts should be actionable.** Below 50% means your alerting needs an overhaul.

## Alert Template

Every alert should include:

```
Title: [Service] [Symptom] [Severity]
Body:
  What: [What is happening]
  Impact: [Who is affected]
  Runbook: [Link to runbook]
  Dashboard: [Link to relevant dashboard]
  Recent changes: [Link to deploy log]
```

## Related

- `research/monitoring/slo-sli-guide.md` — Alert based on SLO burn rate
- `research/incidents/oncall-rotation.md` — On-call burnout prevention
- `research/incidents/incident-classification.md` — Severity levels
- `entities/pagerduty.md` — PagerDuty alert configuration
- `entities/datadog.md` — Datadog monitor setup
