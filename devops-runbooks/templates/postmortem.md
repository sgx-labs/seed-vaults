---
title: "Template — Blameless Post-Mortem"
tags: [template, post-mortem, blameless, review, action-items]
content_type: template
domain: operations
---

# Post-Mortem Template

*Copy this template within 48 hours of incident resolution. Fill it out collaboratively.*

---

## Incident Overview

- **Incident ID:** INC-YYYY-NNN
- **Date:** YYYY-MM-DD
- **Severity:** P0 / P1 / P2
- **Duration:** X hours Y minutes
- **Time to detect (MTTD):** X minutes
- **Time to resolve (MTTR):** X hours Y minutes
- **Incident Commander:** [Role]
- **Post-Mortem Author:** [Role]

## Summary

[2-3 sentences: what happened, what was the impact, how was it resolved]

## Impact

- **Users affected:** [Number/percentage]
- **Services degraded:** [List]
- **Revenue impact:** [If applicable]
- **SLO impact:** [Error budget consumed]

## Timeline

| Time (UTC) | Event |
|------------|-------|
| HH:MM | [Triggering event] |
| HH:MM | [First symptom observed] |
| HH:MM | [Alert fired] |
| HH:MM | [On-call acknowledged] |
| HH:MM | [Escalation occurred] |
| HH:MM | [Root cause identified] |
| HH:MM | [Fix applied] |
| HH:MM | [Full recovery confirmed] |

## Root Cause

[Detailed technical explanation of what caused the incident. Focus on systems and processes, not individuals.]

## Contributing Factors

1. [Factor 1 — e.g., "No automated rollback was configured"]
2. [Factor 2 — e.g., "Alert threshold was set too high to catch gradual degradation"]
3. [Factor 3 — e.g., "Runbook for this scenario did not exist"]

## What Went Well

1. [e.g., "On-call responded within 3 minutes"]
2. [e.g., "Monitoring detected the issue before users reported it"]
3. [e.g., "Communication to stakeholders was timely and clear"]

## What Could Be Improved

1. [e.g., "Detection took 20 minutes — should have alerted at 5% error rate"]
2. [e.g., "Runbook was outdated and missing recent infrastructure changes"]
3. [e.g., "Rollback required manual steps that added 15 minutes to MTTR"]

## Action Items

| Action | Owner | Priority | Deadline | Status |
|--------|-------|----------|----------|--------|
| [Specific, measurable action] | [Role] | P0/P1/P2 | YYYY-MM-DD | Open |
| [Another action] | [Role] | P0/P1/P2 | YYYY-MM-DD | Open |

## Lessons Learned

[Key takeaways for the team and organization. What would you tell someone who has never seen this failure mode?]

## References

- Incident report: [Link to incident report]
- Related post-mortems: [Links to similar past incidents]
- Runbook updates: [Links to updated runbooks]
