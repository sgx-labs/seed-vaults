---
title: "Technical Decision-Making"
tags: [technical-decisions, rfc, adr, decision-making, architecture, consensus, governance]
content_type: hub
domain: engineering-management
---

# Technical Decision-Making

## Overview

As an engineering manager, you'll face a constant tension: decide too fast and you miss important input. Decide too slow and you become a bottleneck. The skill isn't making perfect decisions -- it's knowing which decisions need which process.

## The Decision Spectrum

### Type 1: Irreversible, High Impact — Use an RFC
- Choosing a new database, rewriting a core service, changing the API contract
- Get broad input, time-box discussion (1-2 weeks), document the decision
- Examples: "Migrate from PostgreSQL to CockroachDB," "Adopt GraphQL for public API"

### Type 2: Reversible, High Impact — Decide with a Small Group
- Choosing a framework, reorganizing code structure, adopting a new tool
- Get input from the 2-3 people most affected, decide within days
- Examples: "Switch from Jest to Vitest," "Reorganize the monorepo"

### Type 3: Reversible, Low Impact — Let the Individual Decide
- Naming conventions, test patterns, local tooling
- The person doing the work decides. Don't waste group time.
- Examples: "Use interfaces vs types in TypeScript," "Which logging library"

## The RFC Process

For Type 1 decisions, use a lightweight RFC (Request for Comments):

```
# RFC: [Title]

**Author**: [Name]
**Status**: Draft | Open for Comment | Accepted | Rejected
**Date**: YYYY-MM-DD
**Decision deadline**: YYYY-MM-DD

## Context
[What situation prompted this decision? 2-3 sentences.]

## Proposal
[What you want to do. Be specific enough to evaluate, brief enough to read.]

## Alternatives Considered
[What else was evaluated. Why each was rejected.]

## Tradeoffs
[What we gain. What we give up. What risks we accept.]

## Rollback Plan
[If this doesn't work, how do we undo it?]
```

**RFC Rules:**
- Time-box feedback: 5 business days maximum. Silence = consent.
- The author makes the final call, not a committee. Disagree-and-commit.
- Publish to the whole team. Don't hide decisions in DMs.

## Architecture Decision Records (ADRs)

For decisions that are made (whether through RFC or not), record them:

```
# ADR-001: Use PostgreSQL for primary data store

**Status**: Accepted
**Date**: 2025-03-15
**Deciders**: [Names]

**Context**: We need a primary database. Our data is relational, we need ACID transactions, and the team has PostgreSQL experience.

**Decision**: Use PostgreSQL 16 on RDS.

**Consequences**: We get strong consistency and familiar tooling. We give up horizontal scaling simplicity (would need Citus or partitioning for >1TB).
```

Keep ADRs in the repo. They're code artifacts, not wiki pages.

## When NOT to Use This Framework

- **Don't RFC trivial decisions**: If someone RFCs the color of a button, your process is too heavy.
- **Don't let RFCs become gatekeeping**: The goal is input, not permission. If every change needs an RFC, people will stop innovating.
- **Don't use consensus as a crutch**: "Everyone agreed" is not a reason. "Here's the evidence and tradeoffs" is.

## In Reality

The biggest failure mode isn't making wrong decisions -- it's not making decisions at all. "Let's revisit this next week" repeated for months is a decision to do nothing, and it's often the worst one.

When I managed a team that debated their caching strategy for three months, we were losing users to slow page loads the entire time. I finally said "We're going with Redis. If it's wrong, we'll switch. The cost of switching is lower than the cost of another month of debate." It worked fine. The decision quality mattered less than the decision speed.

## Your Role as Manager

You're not the technical authority (unless you genuinely are the most qualified). Your role:
1. Ensure the right people are involved
2. Ensure decisions get made in a reasonable timeframe
3. Ensure decisions are documented
4. Break ties when the team is deadlocked
5. Take accountability for the outcome regardless of who decided

## See Also

- `entities/adr-template.md` -- Ready-to-use ADR template
- `guides/setting-up-rfc-process.md` -- Step-by-step RFC process setup
- `hub/cross-team-collaboration.md` -- Decisions that span team boundaries
- `hub/meeting-management.md` -- Running decision meetings effectively
- `research/build-vs-buy.md` -- Framework for a common Type 1 decision
