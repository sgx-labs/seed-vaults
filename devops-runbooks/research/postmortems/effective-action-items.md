---
title: "Writing Effective Post-Mortem Action Items"
tags: [post-mortem, action-items, follow-up, improvement, SRE]
content_type: research
domain: operations
---

# Writing Effective Post-Mortem Action Items

## The Question

How do I write post-mortem action items that actually get done and prevent recurrence?

## The Problem

Most post-mortem action items are too vague, have no owner, and never get done. Six months later, the same incident happens.

## Good vs. Bad Action Items

| Bad | Good |
|-----|------|
| "Improve monitoring" | "Add p99 latency alert for /api/checkout with threshold 2s, route to #payments-oncall" |
| "Be more careful with deploys" | "Add automated rollback trigger when error rate >5% within 5 min of deploy" |
| "Fix the bug" | "Add input validation for order quantity field to reject negative values (ticket JIRA-1234)" |
| "Update runbook" | "Add database connection pool exhaustion to runbook research/runbooks/database-outage.md with specific commands for killing idle connections" |

## Action Item Template

Each action item should have:

```
WHAT:     [Specific, measurable action]
WHY:      [Which contributing factor this addresses]
OWNER:    [One person/role — not "the team"]
PRIORITY: [P0: this week / P1: this sprint / P2: this quarter]
DEADLINE: [Specific date]
TICKET:   [Issue tracker reference]
STATUS:   [Open / In Progress / Done]
```

## Categories of Action Items

### 1. Detection (Reduce MTTD)

"How do we find out faster?"

```
- Add alert for [metric] with threshold [value]
- Add synthetic check for [endpoint/flow]
- Add log-based alert for [error pattern]
- Add dashboard panel for [metric]
```

### 2. Response (Reduce MTTR)

"How do we fix it faster?"

```
- Create/update runbook for [scenario]
- Add automated rollback for [condition]
- Pre-provision failover capacity for [service]
- Add health check endpoint to [service]
```

### 3. Prevention (Reduce Frequency)

"How do we stop it from happening?"

```
- Add input validation for [field/parameter]
- Add circuit breaker for [downstream dependency]
- Add rate limiting for [endpoint]
- Add automated test for [scenario]
- Add pre-deploy check for [condition]
```

### 4. Process

"How do we improve our process?"

```
- Add [check] to deploy checklist
- Update escalation policy to include [role]
- Schedule quarterly disaster recovery drill
- Add [topic] to on-call training
```

## Tracking Action Items

Track completion rate as a team metric:

```
Action Item Completion Rate = (completed / total) * 100

Target: >80% within their deadlines

If <50%: Action items are too ambitious or not prioritized.
Fix: Reduce scope per item, increase priority, assign dedicated time.
```

## Common Pitfalls

1. **Too many action items.** Pick the top 3-5. A post-mortem with 20 action items will complete zero.
2. **"Team" ownership.** If everyone owns it, nobody owns it. Assign one person.
3. **No deadline.** "When we get to it" means never. Set a date.
4. **Action items not in issue tracker.** If it is not tracked, it will not get done.
5. **All prevention, no detection.** You cannot prevent everything. Improve detection too.

## Follow-Up Process

1. Action items go into the issue tracker within 24 hours of the post-mortem
2. Weekly check-in: are items on track?
3. Monthly review: what percentage of post-mortem items are completed?
4. Quarterly audit: did any repeat incidents occur because items were not completed?

## Related

- `research/postmortems/blameless-postmortem.md` — Full post-mortem guide
- `templates/postmortem.md` — Post-mortem template
- `research/incidents/incident-metrics.md` — Tracking improvement
- `hub/post-mortems.md` — Post-mortem overview
