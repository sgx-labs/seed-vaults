---
title: "Creating a Team Charter"
tags: [guide, team-charter, purpose, norms, ownership, alignment, formation]
content_type: guide
domain: engineering-management
---

# Creating a Team Charter

## What a Team Charter Is

A team charter is a short document that answers: Who are we? What do we own? How do we work? It aligns the team on purpose and expectations. It's especially valuable when forming a new team, after a reorg, or when team norms have drifted.

## Step 1: Draft (Manager Does This)

Prepare a first draft. Don't make the team start from a blank page -- that's slow and frustrating. You'll refine it together.

```markdown
# [Team Name] Charter

## Mission
[One sentence: Why does this team exist? What value do we deliver?]

## Scope
**We own:**
- [Service/product/area 1]
- [Service/product/area 2]

**We don't own (but get confused for):**
- [Common confusion area — who actually owns it]

## Team Members
| Name | Role | Focus Area |
|------|------|------------|
| [Name] | [Title] | [Primary responsibility] |

## How We Work
- **Sprint cadence**: [Length, start day]
- **Standups**: [Time, format]
- **Code review**: [Expectations: turnaround time, who reviews]
- **On-call**: [Rotation, escalation path]
- **Communication**: [Primary channel, response expectations]

## Working Agreements
- [Agreement 1: e.g., "PRs get reviewed within 4 hours during business hours"]
- [Agreement 2: e.g., "We don't merge our own PRs"]
- [Agreement 3: e.g., "On-call pages get acknowledged within 15 minutes"]

## Success Metrics
- [Metric 1: e.g., "Deployment frequency: daily"]
- [Metric 2: e.g., "SEV1 incidents: < 2 per quarter"]
- [Metric 3: e.g., "Sprint commitment completion: > 80%"]

## Stakeholders
| Who | What they need from us | How we communicate |
|-----|----------------------|-------------------|
| [Product manager] | Feature delivery | Sprint review |
| [Other team] | API stability | Slack + RFC for breaking changes |

## Decision-Making
- [How we make technical decisions: see hub/technical-decision-making.md]
- [What the team decides vs what the manager decides]
```

## Step 2: Workshop (Team Does This Together)

Schedule 60 minutes with the full team. Walk through each section:

1. **Mission**: "Does this capture why we exist? Would you explain our team this way to a new hire?"
2. **Scope**: "Is there anything we own that isn't listed? Anything listed that we shouldn't own?"
3. **Working agreements**: "What rules do we want to hold each other to? What happens when we break them?"
4. **Success metrics**: "Are these the right things to measure? Are the targets achievable?"

Encourage debate. The charter is only useful if everyone believes in it.

## Step 3: Publish and Reference

- Store the charter somewhere the team sees it regularly (team wiki, README, team channel topic)
- Reference it in onboarding: new hires read it in their first week
- Review it quarterly: "Is this still accurate? What should change?"

## When NOT to Create a Charter

- **Solo engineers or pairs**: If the "team" is 1-2 people, a charter is overhead. A brief README suffices.
- **Temporary project teams**: If the team disbands in 4 weeks, don't charter it. Just align on goals and communication.
- **As a substitute for leadership**: A charter doesn't replace active management. If the team is struggling, a document won't fix it.

## See Also

- `hub/meeting-management.md` -- Running the charter workshop
- `research/engineering-culture-manifestos.md` -- Team charters as micro-culture docs
- `hub/cross-team-collaboration.md` -- Scope boundaries with other teams
- `entities/team-topology-patterns.md` -- Team types and their purposes
- `hub/onboarding-engineers.md` -- Charter as onboarding material
