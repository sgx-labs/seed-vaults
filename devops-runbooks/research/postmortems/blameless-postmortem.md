---
title: "Blameless Post-Mortem Guide"
tags: [post-mortem, blameless, incident-review, culture, SRE]
content_type: research
domain: operations
---

# Blameless Post-Mortem Guide

## The Question

How do I write and facilitate a blameless post-mortem?

## Core Principle

A blameless post-mortem assumes everyone involved had good intentions and made the best decisions they could with the information available. The goal is to improve systems and processes, not to find someone to punish.

## What "Blameless" Actually Means

| Blameless | Blameful |
|-----------|----------|
| "The deploy pipeline lacked automated rollback" | "The engineer forgot to test" |
| "Monitoring did not alert on this failure mode" | "Nobody was watching the dashboard" |
| "The runbook was outdated and missing steps" | "The on-call should have known better" |
| "How did this change reach production without catching the regression?" | "Why did you push that code?" |

## Post-Mortem Timeline

```
Incident resolved
  ↓ Within 24 hours
Facilitator assigned, timeline drafted
  ↓ Within 48 hours
Post-mortem meeting (60 minutes)
  ↓ Within 72 hours
Document finalized with action items
  ↓ Within 1 week
Action items in issue tracker with owners and deadlines
  ↓ Within 2 weeks
Document shared with wider organization
```

## Meeting Structure (60 Minutes)

### 1. Timeline walkthrough (15 min)

Walk through what happened chronologically. No interpretation, just facts.

**Facilitator script:**
"Let us walk through what happened. I will share the timeline, and anyone who was involved can add details or corrections. We are not looking for blame — we are building an accurate picture of what happened."

### 2. Impact assessment (5 min)

Quantify the impact:
- Users affected (number or percentage)
- Duration of impact
- Revenue impact (if applicable)
- Error budget consumed
- Data affected (if any)

### 3. Contributing factors (20 min)

Use the "Five Whys" technique, but apply it to systems, not people.

```
Observation: Payment API returned 500 errors for 45 minutes.
Why? → Database connection pool was exhausted.
Why? → A new query was running without connection limits.
Why? → The code review did not catch the unbounded query.
Why? → There is no automated check for connection pool impact.
Why? → Connection pool monitoring was never set up for this service.

Root cause: Missing connection pool monitoring and query review guidelines.
```

### 4. What went well (5 min)

This is not optional. Celebrating what worked reinforces good practices.

Examples:
- "Alert fired within 2 minutes of the issue starting"
- "On-call had the runbook ready and followed it"
- "Communication to stakeholders was timely"

### 5. Action items (15 min)

Every action item must be:
- **Specific:** "Add connection pool metric to Datadog" not "improve monitoring"
- **Owned:** One person responsible (by role, not name in public docs)
- **Deadlined:** A specific date, not "soon"
- **Tracked:** In the issue tracker, not just in the document

### Prioritize action items

| Priority | Fix | Timeline |
|----------|-----|----------|
| **P0** | Prevent exact same incident from recurring | This week |
| **P1** | Prevent similar incidents | This sprint |
| **P2** | Improve detection or response time | This quarter |

## Facilitation Tips

1. **Interrupt blame language immediately.** "Let us focus on the system — what allowed this to happen?"
2. **Use "we" not "you."** "How did we miss this?" not "How did you miss this?"
3. **Silence is okay.** People need time to think. Do not fill every pause.
4. **Document in real time.** Everyone should see the document being written.
5. **End with action items.** No post-mortem is complete without next steps.

## Anti-Patterns

| Anti-Pattern | Problem | Fix |
|-------------|---------|-----|
| Naming individuals as root cause | Creates fear, suppresses reporting | Focus on systems and processes |
| No action items | Nothing changes, incident repeats | Require concrete follow-ups |
| Action items with no owner | Nobody does them | Assign one owner per item |
| Only doing post-mortems for P0 | Miss learning from smaller incidents | Post-mortem all P0-P2 |
| Writing but not sharing | Organization does not learn | Publish to engineering-wide channel |

## Related

- `templates/postmortem.md` — Post-mortem template to fill out
- `research/postmortems/effective-action-items.md` — Writing good action items
- `research/postmortems/facilitation-guide.md` — Detailed facilitation guide
- `research/incidents/incident-metrics.md` — Tracking improvement over time
- `hub/post-mortems.md` — Post-mortem overview
