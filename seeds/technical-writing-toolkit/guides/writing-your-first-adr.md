---
title: "Writing Your First ADR"
tags: [guide, adr, decision, getting-started, step-by-step, documentation]
content_type: guide
domain: technical-writing
---

# Writing Your First ADR

## When to Use This Guide

You've heard about Architecture Decision Records and want to start using them. This guide walks you through writing your first one.

## Step 1: Pick a Decision (5 minutes)

Your first ADR should be a retroactive one -- a decision your team already made that people ask about. Common candidates:

- "Why did we choose PostgreSQL over MongoDB?"
- "Why do we use REST instead of GraphQL?"
- "Why do we deploy on AWS instead of GCP?"
- "Why did we write this in Go instead of Python?"

Pick the decision your team discusses most often.

## Step 2: Create the File (2 minutes)

```bash
mkdir -p docs/decisions
touch docs/decisions/001-use-postgresql.md
```

Use sequential numbering. Use a descriptive filename that starts with the number.

## Step 3: Write the ADR (15 minutes)

Open the file and fill in this template:

```markdown
# ADR-001: Use PostgreSQL for Primary Database

**Status:** Accepted (retroactive)
**Date:** 2025-03-15
**Deciders:** Backend team

## Context

We needed a primary database for our application. The data model
is relational with complex queries across multiple tables. We
expected 10K-100K rows in the first year, growing to 1M+ by year
two. The team had prior PostgreSQL experience.

## Decision

Use PostgreSQL 15 as the primary database, hosted on AWS RDS.

## Alternatives Considered

**MongoDB** — Our data is relational with many joins. A document
store would require denormalization and application-level joins.
The team had limited MongoDB experience.

**MySQL** — Similar capability, but the team had deeper PostgreSQL
knowledge. PostgreSQL's JSONB support lets us handle semi-structured
data without a separate document store.

## Consequences

- Team can leverage existing PostgreSQL expertise
- Complex queries are well-supported
- JSONB columns handle semi-structured metadata without a second database
- Schema migrations require tooling (we use golang-migrate)
- RDS costs are slightly higher than self-managed alternatives
```

## Step 4: Get Feedback (10 minutes)

Share the ADR with one or two colleagues who were involved in the original decision:

- "Does this accurately capture why we chose PostgreSQL?"
- "Am I missing any alternatives we considered?"
- "Are the consequences right?"

## Step 5: Commit and Start the Log (5 minutes)

Create a decision log alongside your ADR:

```bash
touch docs/decisions/README.md
```

```markdown
# Architecture Decision Records

| # | Decision | Status | Date |
|---|----------|--------|------|
| 001 | [Use PostgreSQL](001-use-postgresql.md) | Accepted | 2025-03-15 |
```

Commit both files:

```bash
git add docs/decisions/
git commit -m "Add first ADR: Use PostgreSQL for primary database"
```

## Step 6: Build the Habit

Your first ADR is done. Now build the habit:

- **Next time someone asks "why do we..."** -- write an ADR for the answer
- **Next time the team makes a decision** -- write the ADR before or right after
- **In PR reviews** -- if a PR makes an architectural choice, ask "should this be an ADR?"
- **In design discussions** -- "Let's capture this as an ADR"

## Total Time

About 35 minutes for your first ADR. After a few, you'll write them in 15 minutes.

## Common First-ADR Mistakes

- **Too long.** Your first ADR shouldn't exceed one page. If it does, you're including too much context.
- **No alternatives.** The value of an ADR is showing what you *didn't* choose. "We chose X" isn't useful without "instead of Y because..."
- **Too formal.** ADRs aren't academic papers. Write like you're explaining the decision to a new team member.
- **Waiting for the perfect decision.** Your first ADR doesn't need to be your most important decision. Just pick one and start.

## See Also

- `hub/architecture-decision-records.md` -- ADR philosophy and patterns
- `hub/rfcs-and-design-docs.md` -- For decisions that haven't been made yet
- `entities/common-documentation-templates.md` -- ADR template
- `decisions/log.md` -- This seed's own decision log
