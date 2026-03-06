---
title: "Incident Management"
tags: [incident, postmortem, severity, on-call, SRE, outage, blameless, response]
content_type: hub
domain: engineering-management
---

# Incident Management

## Overview

Every team will have incidents. The question isn't whether -- it's how you respond. A good incident management process minimizes damage during the incident and maximizes learning after it. A bad one creates blame, panic, and repeat failures.

## Severity Levels

Define these before you need them:

| Level | Name | Criteria | Response |
|-------|------|----------|----------|
| SEV1 | Critical | Revenue impact, data loss, full outage | All hands, exec notified, war room, 15-min updates |
| SEV2 | Major | Significant feature broken, degraded for many users | On-call + team lead, 30-min updates |
| SEV3 | Minor | Feature broken for subset of users, workaround exists | On-call handles, daily updates |
| SEV4 | Low | Cosmetic, minor degradation, no user-facing impact | Next business day, normal ticket |

**The most common mistake**: not defining severity levels until SEV1 hits and everyone's arguing about whether it's "really that bad."

## Incident Roles

For SEV1-2 incidents, assign clear roles:

- **Incident Commander (IC)**: Coordinates response, makes decisions, communicates status. Does NOT debug.
- **Technical Lead**: Leads diagnosis and fix. The person with the deepest relevant knowledge.
- **Communications Lead**: Updates stakeholders, status page, customers. Shields the IC and tech lead from interruptions.
- **Scribe**: Documents timeline, decisions, actions in real-time. Invaluable for the postmortem.

**Key rule**: The Incident Commander is not the most senior person. It's the person best equipped to coordinate. Rotate this role to build the skill across the team.

## During the Incident

1. **Declare the incident** -- Name it, assign severity, open a dedicated channel
2. **Assign roles** -- IC, tech lead, comms, scribe
3. **Diagnose** -- Follow runbooks if they exist, check dashboards, review recent deploys
4. **Mitigate first, fix later** -- Rollback, feature flag, redirect traffic. Stop the bleeding before finding the root cause.
5. **Communicate regularly** -- Even "still investigating" is better than silence
6. **Document everything** -- Timestamps, hypotheses tested, actions taken

## The Postmortem Template

Run within 48 hours while memory is fresh. The entire involved team attends.

```
# Postmortem: [Incident Title]

**Date**: YYYY-MM-DD
**Severity**: SEV-X
**Duration**: X hours Y minutes
**Impact**: [Users affected, revenue impact, data impact]
**Authors**: [Names]

## Summary
[2-3 sentence overview of what happened and how it was resolved]

## Timeline
- HH:MM — [Event]
- HH:MM — [Event]
- HH:MM — Incident declared
- HH:MM — Root cause identified
- HH:MM — Mitigation applied
- HH:MM — Incident resolved

## Root Cause
[Technical explanation. Be specific. "Human error" is never a root cause -- what system allowed the error to happen?]

## Contributing Factors
[What else made this worse? Missing monitoring? No runbook? Unclear ownership?]

## What Went Well
[What worked in the response? Celebrate this.]

## What Went Poorly
[What slowed us down or made things worse? No blame -- system failures.]

## Action Items
| Action | Owner | Priority | Due Date |
|--------|-------|----------|----------|
| [Specific fix] | [Name] | P1 | [Date] |
| [Monitoring addition] | [Name] | P2 | [Date] |
| [Process improvement] | [Name] | P3 | [Date] |

## Lessons Learned
[What do we know now that we didn't before?]
```

## Blameless Postmortems

"Blameless" doesn't mean "no accountability." It means:
- We assume everyone acted with the best information they had
- We focus on system failures, not individual failures
- "Why did the system allow this?" not "Why did you do that?"
- The goal is learning, not punishment

When I ran a postmortem where the root cause was "an engineer deployed on Friday without testing," the blameless version was: "Our deployment pipeline allows untested code to reach production. We need automated test gates." The engineer wasn't the failure -- the process was.

## When NOT to Use This Framework

- **SEV4 issues**: Don't run a full postmortem for a typo on a settings page. Fix it and move on.
- **If the team is exhausted**: After a 12-hour incident, schedule the postmortem for 2-3 days out, not the next morning.
- **As punishment**: If postmortems feel like interrogations, people will hide incidents.

## See Also

- `guides/running-blameless-postmortem.md` -- Step-by-step postmortem facilitation
- `guides/setting-up-oncall-rotation.md` -- Building sustainable on-call
- `hub/engineering-metrics.md` -- MTTR and incident frequency tracking
- `hub/psychological-safety.md` -- Why blameless culture matters
- `hub/meeting-management.md` -- Running the postmortem meeting effectively
