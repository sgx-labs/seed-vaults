---
title: "Process Change Decision Template"
tags: [decision, process, change, template, improvement, workflow]
content_type: decision
domain: engineering-management
---

# Process Change Decision Template

Use this template when introducing, modifying, or removing a team process (sprint cadence, code review policy, on-call rotation, meeting structure, etc.).

## Template

```markdown
# Process Change: [Title]

**Status**: Proposed | Trial | Accepted | Rejected | Rolled Back
**Date**: YYYY-MM-DD
**Proposed by**: [Name]
**Trial period**: [Duration, e.g., 4 weeks]

## Current Process
[How does it work today? If there's no process, say "No formal process exists."]

## Problem with Current Process
[What's not working? Include evidence, not just vibes.]
- [Problem 1: e.g., "PRs sit for an average of 18 hours before first review"]
- [Problem 2: e.g., "Sprint planning takes 3 hours because backlog isn't refined"]

## Proposed Change
[What will the new process look like? Be specific enough to follow.]

## Why This Change
[What outcome do you expect? How will you measure it?]
- Expected outcome: [e.g., "PR review time drops to under 4 hours"]
- Measurement: [e.g., "Track via GitHub PR metrics for 4 weeks"]

## Trial Plan
- **Start date**: [Date]
- **End date**: [Date]
- **Check-in**: [Midpoint review date]
- **Success criteria**: [Measurable threshold for keeping the change]
- **Rollback trigger**: [What would cause us to revert]

## Team Input
[Summarize feedback from the team. Did they have concerns? What was adjusted
based on their input?]

## Decision After Trial
[Filled in after the trial period]
- **Result**: [What happened? Did it meet success criteria?]
- **Decision**: Keep / Modify / Revert
- **Rationale**: [Why]
```

## Key Principle: Trial Before Permanent

Never make a process change permanent on day one. Run it as a trial for 2-4 weeks, measure, and then decide. This:
- Reduces resistance ("it's just a trial, not permanent")
- Gives you data instead of opinions
- Creates a natural checkpoint to adjust

## When to Use This Template

- Changing sprint cadence or ceremonies
- Introducing or modifying code review policies
- Changing on-call rotation or escalation paths
- Modifying meeting structure or frequency
- Introducing new tools or workflows

## See Also

- `hub/meeting-management.md` -- Meeting process changes
- `hub/sprint-retrospectives.md` -- Where process problems surface
- `hub/engineering-metrics.md` -- Measuring process effectiveness
- `decisions/log.md` -- Log the decision after the trial
