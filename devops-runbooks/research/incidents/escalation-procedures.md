---
title: "Escalation Procedures"
tags: [escalation, incident, on-call, communication, management, incident-commander]
content_type: research
domain: operations
---

# Escalation Procedures

## The Question

When and how do I escalate an incident?

## Escalation Triggers

### Escalate immediately (do not wait)

- P0 incident: any total outage, data loss, or security breach
- You do not have access to fix the issue
- The issue is in a system you do not own
- You are not confident in the fix

### Escalate after 15 minutes

- No progress on diagnosis
- The fix is more complex than expected
- Multiple systems are affected
- Customer impact is growing

### Escalate after 30 minutes

- The runbook does not cover this scenario
- You need vendor support (AWS, database provider)
- Management needs to be aware (revenue impact, customer communications)

## Escalation Path

```
Level 1: On-call engineer
  ↓ (15 min, no progress)
Level 2: Team lead / Senior engineer
  ↓ (30 min, no progress)
Level 3: Engineering manager / Architect
  ↓ (45 min, no progress, or P0)
Level 4: VP Engineering / CTO
  ↓ (1 hour, or active data loss / security breach)
Level 5: CEO (if customer/legal/PR impact)
```

## How To Escalate

### What to say when escalating

```
"I am escalating [incident description].
Severity: P[X]
Impact: [who is affected, how many users]
What I have tried: [list actions taken]
What I need: [specific help needed]
Incident channel: #incident-YYYY-MM-DD"
```

### Escalation channels (in order of urgency)

1. **PagerDuty** — Pages the next person in escalation policy
2. **Phone call** — For P0 when PagerDuty is not working
3. **Slack/Chat** — For P1-P2, tag the person directly
4. **Email** — For follow-up documentation only, never for urgent escalation

```bash
# PagerDuty: Escalate via CLI
pd incident escalate --id INCIDENT_ID --escalation-level 2

# PagerDuty: Reassign to specific person
pd incident reassign --id INCIDENT_ID --user-id USER_ID
```

## Common Escalation Mistakes

| Mistake | Why It Is Bad | Fix |
|---------|--------------|-----|
| Not escalating soon enough | Problem grows, MTTR increases | Set a timer: 15 min = escalate |
| Escalating without context | Delays the next responder | Use the template above |
| Skipping levels | Creates confusion about who is in charge | Follow the escalation path |
| Fear of "bothering" people | They would rather be woken up than find out at 8am | Escalation is expected |
| Escalating without trying | Wastes senior time on trivial issues | Spend 15 min first, follow runbook |

## Incident Commander Role

During P0/P1 incidents, assign an Incident Commander (IC):

**IC responsibilities:**
1. Coordinate the response (who is doing what)
2. Make decisions about mitigation strategy
3. Communicate status updates to stakeholders
4. Keep the timeline document updated
5. Decide when to declare the incident resolved

**IC is NOT:**
- The person doing the debugging
- The person who caused the incident
- Always the most senior person (it is a coordination role)

## Related

- `research/incidents/incident-classification.md` — When to escalate
- `research/incidents/incident-communication.md` — Communication templates
- `research/incidents/oncall-rotation.md` — On-call expectations
- `entities/pagerduty.md` — PagerDuty escalation setup
