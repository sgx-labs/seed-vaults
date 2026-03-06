---
title: "Setting Up an Engineering RFC Process"
tags: [guide, RFC, process, decision-making, governance, proposal, feedback]
content_type: guide
domain: engineering-management
---

# Setting Up an Engineering RFC Process

## When You Need an RFC Process

You need an RFC process when:
- Technical decisions are being made in DMs and not everyone has context
- Teams disagree and there's no structured way to resolve it
- Knowledge leaves when people leave (decisions weren't documented)
- You have 10+ engineers and informal consensus stops scaling

You don't need one when:
- You have fewer than 5 engineers (just talk)
- All decisions are small and reversible

## Step 1: Define Scope

Not everything needs an RFC. Define what does:

**Requires an RFC:**
- New service or system component
- Database schema changes affecting multiple services
- API contract changes that break clients
- Technology adoption (new language, framework, or major library)
- Architecture changes (monolith split, new message queue, etc.)

**Does NOT require an RFC:**
- Bug fixes
- Feature implementations within an existing design
- Refactoring that doesn't change interfaces
- Individual tooling or workflow preferences

Write this down and share it. If people don't know when to write an RFC, they won't write them.

## Step 2: Create the Template

Keep it short. A template that takes an hour to fill out won't get used.

```markdown
# RFC: [Title]

**Author**: [Name]
**Date**: [Date]
**Status**: Draft | Open for Comment | Accepted | Rejected | Withdrawn
**Feedback deadline**: [Date, typically 5 business days from Open for Comment]

## Problem
[What problem are we solving? Why now? 2-4 sentences.]

## Proposal
[What you want to do. Specific enough to evaluate, brief enough to read in
10 minutes.]

## Alternatives Considered
[What else was evaluated. Why each was rejected. At least 2 alternatives.]

## Tradeoffs
[What we gain. What we give up. What risks we accept.]

## Implementation Plan
[High-level plan. Phases if applicable. Not a full project plan.]

## Open Questions
[What you're unsure about. What you specifically want feedback on.]
```

## Step 3: Define the Process

1. **Author writes the RFC** and marks status as "Draft"
2. **Author shares with 1-2 trusted reviewers** for early feedback (optional but recommended)
3. **Author marks status as "Open for Comment"** and posts to the team channel
4. **Feedback period**: 5 business days. Everyone can comment.
5. **Author addresses feedback**: Responds to comments, revises if needed
6. **Decision**: Author makes the final call (or a designated decider if scope warrants it)
7. **Status updated**: Accepted, Rejected, or Withdrawn with a brief rationale

**Key rule: Silence is consent.** If someone doesn't comment within the feedback period, they've accepted the proposal. Don't let RFCs languish waiting for universal buy-in.

## Step 4: Store and Index

- Store RFCs in the code repository (`docs/rfcs/` or similar)
- Number them sequentially: `RFC-001`, `RFC-002`
- Accepted RFCs become ADRs (see `entities/adr-template.md`)
- Create an index file linking to all RFCs with status and one-line summary

## Step 5: Review and Iterate

After 3 months:
- How many RFCs were written? (Too few = threshold too high. Too many = threshold too low.)
- Are people actually commenting? (If not, the process isn't working.)
- Are decisions being documented? (If RFCs are written but not concluded, the process has stalled.)

Adjust the process based on what you learn. It should be lightweight enough that people use it willingly.

## See Also

- `hub/technical-decision-making.md` -- When to use RFCs vs other decision processes
- `entities/adr-template.md` -- ADR template for recording decisions
- `hub/cross-team-collaboration.md` -- RFCs that span teams
- `hub/meeting-management.md` -- When an RFC needs a meeting
