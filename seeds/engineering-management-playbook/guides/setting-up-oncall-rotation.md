---
title: "Setting Up On-Call Rotation"
tags: [guide, on-call, rotation, SRE, pager, sustainability, incident-response]
content_type: guide
domain: engineering-management
---

# Setting Up On-Call Rotation

## Prerequisites

Before putting people on-call, ensure:
- Monitoring and alerting exist for critical paths
- Runbooks exist for the most common alerts
- An escalation path is defined (who to call when the on-call can't resolve it)
- Compensation or time-off policy for on-call is defined

If these don't exist, fix them first. On-call without runbooks is just anxiety.

## Step 1: Define the Rotation

### Who's In the Rotation
- **Minimum**: 4 people. Fewer than 4 means someone is on-call more than 1 week per month, which leads to burnout.
- **New engineers**: Shadow the rotation for 2 cycles before carrying the pager solo.
- **Managers**: Include yourself in the rotation (at least initially). It builds empathy and keeps you connected to operational reality.

### Schedule
- **Weekly rotation**: Handoff on a weekday morning (Tuesday or Wednesday -- avoid Monday chaos and Friday handoffs)
- **Handoff ritual**: Outgoing on-call writes a brief handoff note: active issues, things to watch, pending follow-ups
- **Primary + secondary**: If you have enough people, designate a secondary on-call as backup

### Hours
- **Business hours on-call** (recommended for most teams): Respond within 15 minutes, 9am-6pm local time
- **After-hours on-call**: Only if you have user-facing services that require 24/7 uptime
- **Follow-the-sun**: For distributed teams, split on-call across timezones to avoid night pages

## Step 2: Set Up Alerting

### Alert Tiers

| Tier | Urgency | Response | Notification |
|------|---------|----------|-------------|
| P1 | Service down, data loss risk | Immediate (< 15 min) | Page (PagerDuty, Opsgenie) |
| P2 | Degraded service, workaround exists | 1 hour | Page during business hours, queue after-hours |
| P3 | Non-urgent, no user impact | Next business day | Slack/email notification |

### Alert Hygiene
- **Every alert must be actionable.** If an alert fires and the response is "ignore it," delete the alert.
- **Track alert volume per rotation.** If it's increasing, your systems are degrading.
- **Deduplicate.** One incident shouldn't generate 50 pages.

## Step 3: Create Runbooks

For each alert, write a runbook:

```
## Alert: [Alert Name]

**Severity**: P1 / P2 / P3
**Service**: [Service name]
**What it means**: [Plain English: what's happening]
**Impact**: [Who's affected and how]

**Steps to Diagnose**:
1. [Check dashboard X]
2. [Run command Y]
3. [Look for Z in logs]

**Steps to Mitigate**:
1. [Common fix 1]
2. [Common fix 2]
3. [If neither works, escalate to ___]

**Escalation**: [Who to call if you can't resolve it]
**Last updated**: [Date]
```

## Step 4: Compensate Fairly

On-call is real work. Compensate it:
- **Time off**: Day off after a rotation week (or after being paged after hours)
- **Stipend**: Fixed weekly on-call payment (common: $200-500/week)
- **Both**: Many companies offer both

Whatever you choose, document it and apply it consistently. On-call without compensation breeds resentment.

## Step 5: Review Monthly

In your team retro or a dedicated on-call review:
- How many pages this month? Trending up or down?
- Any alert that fired > 3 times without a permanent fix?
- Any gaps in runbooks that were felt during incidents?
- On-call satisfaction: "How was your rotation?"

## See Also

- `hub/incident-management.md` -- Incident response during on-call
- `guides/running-blameless-postmortem.md` -- After-incident learning
- `research/burnout-detection-prevention.md` -- On-call as a burnout risk
- `hub/team-health-indicators.md` -- On-call volume as health signal
- `hub/engineering-metrics.md` -- MTTR and incident tracking
