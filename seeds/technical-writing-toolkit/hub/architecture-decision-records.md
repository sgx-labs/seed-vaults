---
title: "Architecture Decision Records"
tags: [adr, decisions, architecture, template, governance, documentation]
content_type: hub
domain: technical-writing
---

# Architecture Decision Records

## What Is an ADR?

An Architecture Decision Record captures a single architectural decision: what was decided, why, what alternatives were considered, and what the consequences are. ADRs are short (1-2 pages), numbered, and immutable once accepted.

The key insight: decisions are the most valuable documentation you can write. Code shows *what*. Comments show *how*. ADRs show *why*.

## When to Write an ADR

Write an ADR when:
- You choose between two or more reasonable approaches
- The decision will be hard to reverse later
- Future developers will ask "why did we do it this way?"
- You're adopting or dropping a technology, framework, or pattern

Don't write an ADR for:
- Obvious choices with no real alternative
- Decisions that are trivially reversible
- Style preferences (use a style guide instead)

## The Format

```markdown
# ADR-001: Use PostgreSQL for primary data store

**Status:** Accepted
**Date:** 2025-03-15
**Deciders:** Backend team

## Context

We need a primary database for the application. The data is relational
with complex queries. We expect 10K-100K rows in the first year.

## Decision

Use PostgreSQL.

## Alternatives Considered

**MySQL** -- Similar capability, but our team has deeper PostgreSQL
experience. PostGIS support matters for future geo features.

**MongoDB** -- Our data is relational. Document store would require
denormalization and manual joins.

## Consequences

- Team can use existing PostgreSQL expertise
- PostGIS available if we add location features
- Need to manage schema migrations (using golang-migrate)
- Hosting cost is slightly higher than MySQL on some providers
```

## Where to Store ADRs

```
docs/
  decisions/
    001-use-postgresql.md
    002-adopt-rest-over-graphql.md
    003-deploy-on-fly-io.md
    template.md
```

Number them sequentially. Never renumber. If a decision is superseded, add a "Superseded by" line to the old ADR and reference it from the new one.

## Finding Old Decisions

The hardest part of ADRs isn't writing them -- it's finding them later. Make them findable:

- **Number them** -- `ADR-001`, `ADR-002` gives you a canonical reference
- **Use descriptive filenames** -- `003-deploy-on-fly-io.md`, not `003.md`
- **Keep a log** -- a single file listing all ADRs with one-line summaries (see `decisions/log.md`)
- **Reference from code** -- `// See ADR-007 for why we use polling instead of webhooks`

## Retroactive ADRs

Starting ADRs on a mature project? Write retroactive ADRs for the 3-5 decisions people ask about most:

1. Ask the team: "What decisions do new hires always question?"
2. Check PR comments and Slack history for past debates
3. Write the ADR as if you wrote it at decision time, but note "Status: Accepted (retroactive)"

## Common ADR Mistakes

- **Too long.** If your ADR is over 2 pages, split it or trim the context.
- **Missing alternatives.** The value is in showing what you *didn't* choose and why.
- **No consequences.** Every decision has tradeoffs. Name them.
- **Treating them as proposals.** ADRs record decisions that were made. For proposals, use RFCs.
- **Editing accepted ADRs.** ADRs are immutable. Supersede them with new ADRs.

## See Also

- `hub/rfcs-and-design-docs.md` -- For proposals that haven't been decided yet
- `entities/common-documentation-templates.md` -- ADR template
- `guides/writing-your-first-adr.md` -- Step-by-step walkthrough
- `decisions/log.md` -- This seed's own decision log
