---
title: "Incident Classification — P0 Through P4"
tags: [incident, classification, severity, P0, P1, P2, P3, P4, escalation]
content_type: research
domain: operations
---

# Incident Classification (P0-P4)

## The Question

How do I classify incidents and decide who gets paged?

## Classification Matrix

### P0 — Critical (Total Outage)

**Criteria (any one):**
- Complete service outage for all users
- Data loss or data corruption confirmed
- Security breach with active exploitation
- Payment processing completely down

**Response:**
- Page immediately: on-call + engineering lead + VP Eng
- Incident commander assigned within 5 minutes
- Status page updated within 10 minutes
- All-hands bridge call opened
- Post-mortem mandatory within 48 hours

### P1 — High (Major Degradation)

**Criteria (any one):**
- >25% of users affected by errors or degradation
- Core feature completely broken (login, search, checkout)
- Performance degraded >5x normal (p99 latency)
- Single region/AZ outage with no automatic failover

**Response:**
- Page on-call engineer + engineering lead
- Acknowledge within 5 minutes
- Escalate if no progress in 15 minutes
- Status page updated within 15 minutes
- Post-mortem mandatory within 72 hours

### P2 — Medium (Partial Degradation)

**Criteria (any one):**
- <25% of users affected
- Non-core feature broken
- Workaround available
- Internal tools down

**Response:**
- Page on-call engineer
- Respond within 1 hour
- Escalate if no progress in 2 hours
- Post-mortem recommended

### P3 — Low (Minor Issue)

**Criteria:**
- Cosmetic issues affecting small user segment
- Non-critical background jobs failing
- Monitoring gaps identified (no active impact)

**Response:**
- Create ticket, assign to team
- Fix next business day
- No paging required

### P4 — Informational

**Criteria:**
- Technical debt identified
- Performance optimization opportunity
- Documentation gap

**Response:**
- Add to backlog
- Address in sprint planning
- No urgency

## Decision Flowchart

```
Is the site/service completely down?
  YES → P0

Are >25% of users affected?
  YES → P1

Is a core feature broken (no workaround)?
  YES → P1

Is there a workaround?
  YES → P2

Is user impact minimal/cosmetic?
  YES → P3

No current user impact?
  → P4
```

## Common Mistakes

1. **Under-classifying to avoid pages.** If it meets P1 criteria, classify it P1.
2. **Over-classifying to get attention.** P0 means ALL users are affected.
3. **Re-classifying mid-incident.** Only re-classify with new information, not wishful thinking.
4. **Classifying by cause instead of impact.** A tiny bug can be P0 if it breaks payments.

## Related

- `hub/incident-response.md` — Full incident response overview
- `research/incidents/incident-communication.md` — How to communicate during incidents
- `research/incidents/incident-metrics.md` — Tracking MTTR, MTTD
- `entities/pagerduty.md` — Setting up escalation policies
