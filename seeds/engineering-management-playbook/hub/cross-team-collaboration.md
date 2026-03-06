---
title: "Cross-Team Collaboration"
tags: [cross-team, dependencies, stakeholders, SLA, collaboration, coordination, alignment]
content_type: hub
domain: engineering-management
---

# Cross-Team Collaboration

## Overview

Most engineering problems aren't technical -- they're coordination problems. When two teams need to work together, the default mode is chaos: unclear ownership, surprise dependencies, and passive-aggressive Slack threads. This note gives you frameworks for making cross-team work actually work.

## Dependency Management

### The Dependency Map

Before every quarter/cycle, map your dependencies:

```
Our Team Needs:          | Who Provides It:       | Status:
Auth service API changes | Platform team           | Not started - need by Week 4
Design review for new UI | Design team             | Scheduled Week 2
Data pipeline updates    | Data eng team           | In progress - on track
```

Share this with every team you depend on. Ask them to do the same. Mismatched expectations are the #1 source of cross-team friction.

### The Three Questions for Every Dependency

1. **What exactly do you need?** (Not "help with auth" -- "a new OAuth scope endpoint that returns X by March 15")
2. **What happens if it's late?** (Quantify the impact: "We can't ship the feature, affecting 12K users")
3. **What's the fallback?** (Can you build a temporary workaround? Can you descope?)

## SLAs Between Teams

Internal SLAs aren't bureaucracy -- they're clarity. Simple ones work:

- **API review turnaround**: 3 business days
- **Shared service bug fixes**: SEV1 within 4 hours, SEV2 within 2 business days
- **Design review**: 5 business days from request
- **Security review**: 5 business days, 1 business day for expedited

Write them down. If they don't exist, propose them. "We've been waiting 2 weeks for review" is a fixable process problem, not a people problem.

## Stakeholder Management

### The RACI for Cross-Team Work

For any cross-team project, define:
- **Responsible**: Who does the work? (Usually one team)
- **Accountable**: Who owns the outcome? (One person, not a committee)
- **Consulted**: Who provides input? (Their opinions matter)
- **Informed**: Who needs to know? (Status updates only)

If you can't fill in the RACI, the project isn't ready to start.

### The Escalation Path

When cross-team work stalls:
1. **Direct conversation**: Engineer to engineer, or EM to EM
2. **Joint standup**: 15-min sync between the two teams involved
3. **Manager escalation**: Your manager talks to their manager
4. **Executive escalation**: Only if #1-3 fail and there's real business impact

Most issues die at step 1 if you make it a habit. The problem is when people skip to step 3 or 4 because they're frustrated.

## When NOT to Use This Framework

- **Small, co-located teams**: If everyone's in the same room and working on the same product, RACI is overkill. Just talk.
- **Emergency coordination**: During incidents, throw the process out and coordinate in real-time. Restore process afterward.
- **When the real problem is organizational**: If you constantly need a team that doesn't prioritize your requests, the fix might be restructuring, not better coordination.

## In Reality

Cross-team work is where most deadlines die. The other team has their own priorities, their own sprint, their own fires. Your request is noise to them unless you've made the impact and urgency clear.

The best cross-team managers I've worked with do one thing: they build relationships with their counterparts BEFORE they need something. A coffee chat today prevents a blocked sprint next month.

## See Also

- `hub/meeting-management.md` -- Running cross-team meetings
- `hub/managing-up.md` -- Escalating cross-team issues
- `hub/technical-decision-making.md` -- Decisions that span team boundaries
- `entities/stakeholder-communication-templates.md` -- Templates for cross-team updates
- `research/platform-vs-embedded-model.md` -- Organizational models that reduce cross-team friction
