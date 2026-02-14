---
title: "On-Call Rotation Management"
tags: [on-call, rotation, burnout, scheduling, pagerduty, handoff, follow-the-sun]
content_type: research
domain: operations
---

# On-Call Rotation Management

## The Question

How do I manage on-call rotations without burnout?

## Rotation Structure

### Recommended: Follow-the-Sun (3+ time zones)

```
Team A (Americas):  08:00-16:00 UTC
Team B (Europe):    16:00-00:00 UTC
Team C (Asia-Pac):  00:00-08:00 UTC
```

### Common: Weekly Rotation (single time zone)

```
Week 1: Engineer A (primary), Engineer B (secondary)
Week 2: Engineer B (primary), Engineer C (secondary)
Week 3: Engineer C (primary), Engineer A (secondary)
```

**Minimum team size:** 4-5 engineers per rotation to avoid burnout. With fewer, shifts are too frequent.

## On-Call Engineer Responsibilities

1. **Acknowledge alerts within 5 minutes** (P0/P1)
2. **Follow the runbook** — do not improvise at 3am
3. **Escalate if stuck** — no shame in calling for help after 15 minutes
4. **Document actions** — leave a trail for the post-mortem
5. **Hand off clearly** — brief the next on-call at rotation change

## Preventing Burnout

### Quantitative guardrails

| Metric | Healthy | Concerning | Burnout Risk |
|--------|---------|------------|--------------|
| Pages per week | <5 | 5-15 | >15 |
| Off-hours pages | <2 per week | 2-5 | >5 |
| Page-free nights | >5 per week | 3-5 | <3 |
| Time in on-call | <1 week/month | 1-2 weeks | >2 weeks |

### Structural protections

1. **Compensate on-call time.** Extra pay, time off, or both.
2. **Primary + secondary.** Secondary only paged if primary does not ack in 15 min.
3. **No back-to-back weeks.** At least 2 weeks off between on-call shifts.
4. **Quiet hours engineering.** If a team gets >5 pages/week, halt features and fix reliability.
5. **Swap-friendly culture.** Make it easy to swap shifts without guilt.
6. **Post-on-call debrief.** 15-minute review: what happened, what should be fixed.

## On-Call Handoff Template

At every rotation change, the outgoing engineer sends:

```
ON-CALL HANDOFF — [Date]

Active issues:
- [Issue 1: status, what to watch for]
- [Issue 2: status, owner]

Recent changes:
- [Deploy X went out, monitoring until Y]
- [Config change Z, may see brief alerts]

Known noisy alerts:
- [Alert name: can be ignored because X, ticket filed]

Upcoming maintenance:
- [Scheduled window: date, what, runbook link]
```

## Alert Quality Metrics

Track these weekly:

```
Total alerts:        ___
Actionable alerts:   ___ (required human intervention)
Non-actionable:      ___ (resolved on own, false positive, duplicate)
Signal-to-noise:     ___% (actionable / total)
```

**Target:** >70% signal-to-noise ratio. Below 50% means alert fatigue is a serious risk.

## Related

- `research/monitoring/alert-fatigue.md` — Reducing alert noise
- `research/incidents/incident-classification.md` — What to page for
- `entities/pagerduty.md` — PagerDuty schedule setup
- `research/incidents/incident-metrics.md` — Tracking MTTR, MTTD
