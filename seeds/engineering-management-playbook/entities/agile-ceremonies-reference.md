---
title: "Agile Ceremonies Reference"
tags: [agile, scrum, sprint, ceremonies, standup, planning, review, retro, refinement]
content_type: entity
domain: engineering-management
---

# Agile Ceremonies Reference

## Core Ceremonies

### Sprint Planning
- **Purpose**: Commit to what the team will deliver this sprint
- **Frequency**: Start of each sprint
- **Duration**: 1-2 hours for a 2-week sprint
- **Attendees**: Entire team + product owner
- **Output**: Sprint backlog with committed items

**Tips**: Don't plan to 100% capacity. Leave 20% for unplanned work. If planning takes more than 2 hours, your backlog isn't refined enough.

### Daily Standup
- **Purpose**: Surface blockers, coordinate work, maintain awareness
- **Frequency**: Daily
- **Duration**: 15 minutes max
- **Attendees**: Team members
- **Format**: "What's blocking me?" focus, not status reports

**Tips**: Stand up (literally, if in-person). If a discussion starts, take it offline: "Let's talk after standup." If nothing is blocked, keep it to 5 minutes.

### Sprint Review (Demo)
- **Purpose**: Show stakeholders what was built, get feedback
- **Frequency**: End of each sprint
- **Duration**: 30-60 minutes
- **Attendees**: Team + stakeholders
- **Output**: Stakeholder feedback, direction for next sprint

**Tips**: Demo working software, not slides. Include stakeholders who rarely see the work -- their questions reveal blind spots.

### Sprint Retrospective
- **Purpose**: Reflect on the process, identify improvements
- **Frequency**: End of each sprint (after review)
- **Duration**: 30-60 minutes
- **Attendees**: Team only (no stakeholders)
- **Output**: 2-3 action items with owners

**Tips**: See `hub/sprint-retrospectives.md` for five proven formats. Rotate the facilitator.

### Backlog Refinement (Grooming)
- **Purpose**: Prepare upcoming work for sprint planning
- **Frequency**: Mid-sprint (or continuous)
- **Duration**: 30-60 minutes
- **Attendees**: Team + product owner
- **Output**: Refined, estimated stories ready for planning

**Tips**: Refine 2-3 sprints ahead. If stories aren't clear enough to estimate, they're not refined enough.

## Anti-Patterns

| Anti-Pattern | Symptom | Fix |
|-------------|---------|-----|
| Zombie standup | Same update every day, nobody listens | Switch to async or blockers-only format |
| Planning marathon | 4+ hours of planning | Refine backlog better before planning |
| Retro theater | "Everything's fine" every sprint | Change format, ensure psychological safety |
| Skip the demo | "Nothing to show this sprint" | Ship smaller increments |
| Refinement as planning | Estimating and committing in one meeting | Separate the two events |

## Kanban Alternative

Not every team needs sprints. Kanban works better when:
- Work is continuous (support, operations, maintenance)
- Batch sizes are small and consistent
- Planning horizons don't fit neatly into 2-week blocks

**Kanban essentials**: WIP limits, pull-based flow, continuous delivery, regular reviews.

## See Also

- `hub/sprint-retrospectives.md` -- Retro format deep dive
- `hub/meeting-management.md` -- General meeting effectiveness
- `entities/meeting-cadences.md` -- Full meeting schedules by team size
- `hub/engineering-metrics.md` -- Sprint metrics (velocity, cycle time)
