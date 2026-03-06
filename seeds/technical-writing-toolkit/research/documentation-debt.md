---
title: "Documentation Debt"
tags: [debt, maintenance, staleness, prioritization, quality, documentation]
content_type: research
domain: technical-writing
---

# Documentation Debt

## What It Is

Documentation debt is the accumulation of outdated, missing, or inaccurate documentation. Like technical debt, it compounds. A little doc debt is normal. A lot of it means nobody trusts your docs, which means nobody reads them, which means nobody updates them.

## Signs of Documentation Debt

- Developers say "don't trust the docs, ask Sarah instead"
- New hires take weeks instead of days to get productive
- Support tickets ask questions that should be answered by docs
- README still describes the architecture from two years ago
- Pages have "TODO" or "Coming soon" sections from months ago
- Nobody knows which wiki pages are current
- Multiple pages describe the same thing differently

## Identifying Debt

### Audit Your Documentation

Walk through every documentation page and categorize:

| Category | Action |
|----------|--------|
| **Current and accurate** | No action needed |
| **Mostly accurate, needs minor updates** | Quick fix -- do it now |
| **Significantly outdated** | Rewrite or archive |
| **Redundant (covered elsewhere)** | Merge and redirect |
| **Missing entirely** | Add to backlog |
| **Never referenced** | Consider archiving |

### Automated Detection

- **Last-modified dates.** Pages not updated in 12+ months are suspect.
- **Link checking.** Broken links indicate neglected pages.
- **Search analytics.** High-search, low-satisfaction pages need attention.
- **Code-doc drift.** Compare doc content against current code. SAME's search can help here.

## Prioritizing What to Fix

Not all documentation debt is equal. Prioritize by:

1. **Impact.** How many people are affected? How badly?
2. **Frequency.** How often is this doc accessed or needed?
3. **Risk.** Could outdated information cause an incident?
4. **Effort.** How hard is the fix?

**Priority matrix:**

| | High Impact | Low Impact |
|---|---|---|
| **Easy Fix** | Do immediately | Do when convenient |
| **Hard Fix** | Schedule this sprint | Backlog |

## Paying Down the Debt

### The Boy Scout Rule

"Leave the docs better than you found them." Every PR that changes behavior should update the relevant docs. This prevents new debt from accumulating.

### Documentation Sprints

Dedicate a half-day each quarter to doc debt. The team:
1. Reviews the audit list
2. Each person picks 2-3 items
3. Fix, archive, or rewrite
4. Review each other's changes

### New Hire Documentation Tax

Every new hire updates the onboarding docs as their first PR. They're the best judges of what's missing or unclear -- the problems are fresh.

### Sunset Strategy

Not every doc needs to be maintained forever. Create a sunsetting process:
1. Mark the page as "archived" with a banner
2. Move to an archive section
3. Keep it searchable but clearly labeled as outdated
4. Delete after 12 months if nobody references it

## Prevention

- **Docs in PRs.** Make documentation a checkbox in your PR template.
- **Doc linting in CI.** Catch broken links and formatting issues automatically.
- **Ownership.** Every doc has an owner who reviews it periodically.
- **Review dates.** "Last reviewed: 2025-03-01" on every page.
- **Keep it small.** 20 well-maintained pages beat 200 neglected ones.

## See Also

- `research/measuring-documentation-quality.md` -- Metrics to track debt
- `hub/internal-wiki-best-practices.md` -- Wiki rot is documentation debt
- `research/documentation-as-code.md` -- Repo-based docs are easier to maintain
- `entities/documentation-maturity-model.md` -- Debt reduction across maturity levels
- `guides/starting-documentation-from-zero.md` -- Starting fresh when debt is overwhelming
