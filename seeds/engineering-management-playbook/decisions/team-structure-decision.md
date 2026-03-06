---
title: "Team Structure Decision Template"
tags: [decision, team-structure, reorg, organization, template]
content_type: decision
domain: engineering-management
---

# Team Structure Decision Template

Use this template when making significant changes to team composition, ownership, or reporting structure.

## Template

```markdown
# Team Structure Decision: [Title]

**Status**: Proposed | Accepted | Rejected
**Date**: YYYY-MM-DD
**Proposed by**: [Name]
**Decision maker**: [Name]

## Current State
[How is the team currently structured? Include team sizes, ownership areas,
and reporting lines.]

## Problem
[What's not working? Be specific with evidence.]
- [Problem 1: e.g., "Team A is blocked 30% of the time waiting on Team B"]
- [Problem 2: e.g., "Service X has no clear owner -- incidents take 2x longer"]

## Proposed Change
[What will the new structure look like? Include team sizes, ownership,
and reporting lines.]

## Alternatives Considered
### Option A: [Name]
[Description and why it was rejected]

### Option B: [Name]
[Description and why it was rejected]

## Migration Plan
- **Week 1**: [Communication, announcement]
- **Week 2-3**: [Knowledge transfer, ownership handoff]
- **Week 4**: [New structure fully operational]
- **Week 8**: [Check-in, adjust if needed]

## Risks
- [Risk 1: e.g., "Key engineer may resist the change"]
- [Risk 2: e.g., "Temporary productivity dip during transition"]

## Success Criteria
[How will we know this change worked? Check after 60 and 90 days.]
- [Criteria 1: e.g., "Cross-team blocking reduced by 50%"]
- [Criteria 2: e.g., "Incident response time for Service X reduced to under 1 hour"]

## People Impact
[How does this affect individuals? New managers? New team assignments?
Career implications?]
```

## When to Use This Template

- Splitting a team into two
- Merging teams
- Moving a service or product area to a different team
- Changing reporting lines (new manager, new skip-level)
- Creating a new team

## See Also

- `entities/team-topology-patterns.md` -- Team types reference
- `research/scaling-5-to-50.md` -- Scaling-driven restructuring
- `research/platform-vs-embedded-model.md` -- Platform team decisions
- `hub/cross-team-collaboration.md` -- Cross-team coordination
