---
title: "Architecture Decision Record (ADR) Template"
tags: [ADR, architecture, decision-record, template, documentation, governance]
content_type: entity
domain: engineering-management
---

# Architecture Decision Record (ADR) Template

## Usage

Copy this template for each significant technical or architectural decision. Store ADRs in your repository (e.g., `docs/decisions/` or `adr/`). Number them sequentially.

## Template

```markdown
# ADR-NNN: [Short Title of Decision]

**Status**: Proposed | Accepted | Rejected | Deprecated | Superseded by ADR-XXX
**Date**: YYYY-MM-DD
**Deciders**: [Names of people involved in the decision]

## Context

[What is the issue or situation that motivates this decision? What forces are
at play? Include technical, business, and team considerations. 2-5 sentences.]

## Decision

[What is the change being proposed or decided? Be specific and unambiguous.
This should be a clear statement that someone unfamiliar with the discussion
can understand.]

## Alternatives Considered

### Option A: [Name]
[Description. Why it was considered. Why it was rejected.]

### Option B: [Name]
[Description. Why it was considered. Why it was rejected.]

## Consequences

### Positive
- [What improves as a result of this decision?]
- [What becomes easier?]

### Negative
- [What trade-offs are we accepting?]
- [What becomes harder or more constrained?]

### Risks
- [What could go wrong?]
- [What assumptions are we making?]

## Rollback Plan

[If this decision proves wrong, how do we undo it? Is it reversible? What's
the cost of reversal?]

## References

- [Links to RFCs, design docs, relevant research, or related ADRs]
```

## Tips

- **Short titles**: "Use PostgreSQL for primary data" not "Database technology decision and evaluation process"
- **Living documents**: Update status when decisions change. Don't delete old ADRs -- they're historical records.
- **Keep them small**: One decision per ADR. If the decision has sub-decisions, create separate ADRs.
- **Include "why not"**: The alternatives section is as valuable as the decision itself.
- **Immutable once accepted**: Don't edit the content of accepted ADRs. If the decision changes, create a new ADR that supersedes it.

## See Also

- `hub/technical-decision-making.md` -- When to write an ADR vs RFC
- `decisions/log.md` -- Decision log for this vault
- `guides/setting-up-rfc-process.md` -- RFC process for pre-decision discussion
