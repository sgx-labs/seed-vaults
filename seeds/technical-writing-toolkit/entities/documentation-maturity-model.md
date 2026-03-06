---
title: "Documentation Maturity Model"
tags: [maturity, assessment, levels, improvement, roadmap, entity]
content_type: entity
domain: technical-writing
---

# Documentation Maturity Model

## The Five Levels

### Level 0: No Documentation

**Characteristics:**
- No README or a placeholder README
- Knowledge lives in people's heads
- New hires rely entirely on asking colleagues
- No recorded decisions

**First step:** Write a README with install instructions and a basic example.

### Level 1: Minimal Documentation

**Characteristics:**
- README with basic description and install instructions
- Some code comments, usually inconsistent
- No structured docs beyond the README
- No changelog or decision records

**Signs you're here:** New hires can install the project but need help with everything else.

**Next step:** Add onboarding docs and a CONTRIBUTING guide.

### Level 2: Functional Documentation

**Characteristics:**
- README, CONTRIBUTING, and basic setup guide
- Some API docs (possibly auto-generated)
- Changelog exists but may be inconsistent
- Documentation lives in the repo
- One or two ADRs

**Signs you're here:** Developers can get started without help, but often ask questions the docs could answer.

**Next step:** Add docs-as-code CI checks, start writing ADRs consistently, improve API docs.

### Level 3: Comprehensive Documentation

**Characteristics:**
- Full Diataxis coverage: tutorials, guides, reference, explanation
- Docs-as-code workflow with CI linting and link checking
- Consistent changelog with semantic versioning
- ADRs for significant decisions
- Runbooks for operational tasks
- Style guide for documentation consistency
- Docs reviewed in PRs alongside code

**Signs you're here:** Most questions are answerable from docs. New hires onboard in days, not weeks.

**Next step:** Measure documentation effectiveness, add search analytics, automate more.

### Level 4: Documentation as a Product

**Characteristics:**
- Documentation quality is measured (time-to-success, ticket deflection)
- Docs have a dedicated owner or team
- User feedback collected and acted on
- Documentation is tested (examples verified in CI, links checked)
- Versioned docs for multiple releases
- Internationalization for global audiences
- Regular audits and debt reduction

**Signs you're here:** Documentation is a competitive advantage. Users cite docs as a reason they chose your product.

## Assessment Checklist

| Criterion | L0 | L1 | L2 | L3 | L4 |
|-----------|----|----|----|----|-----|
| README exists | - | Yes | Yes | Yes | Yes |
| Install instructions work | - | Basic | Yes | Yes | Tested in CI |
| API docs | - | - | Partial | Complete | With examples |
| Changelog | - | - | Exists | Consistent | Automated |
| ADRs | - | - | Some | Consistent | With review process |
| Onboarding docs | - | - | Basic | Complete | Measured |
| CI checks | - | - | - | Linting | Linting + testing |
| Quality metrics | - | - | - | - | Active tracking |
| Style guide | - | - | - | Yes | Enforced |
| Feedback loop | - | - | - | - | Active |

## How to Use This

1. Assess your current level honestly
2. Identify the gap between your level and the next
3. Pick the highest-impact item from the "Next step" guidance
4. Implement it
5. Reassess quarterly

Don't try to jump from Level 0 to Level 4. Each level builds on the previous one.

## See Also

- `research/measuring-documentation-quality.md` -- Metrics for Level 3-4
- `research/documentation-debt.md` -- Addressing gaps
- `guides/starting-documentation-from-zero.md` -- Getting from Level 0 to Level 1
- `hub/diataxis-framework.md` -- Framework for Level 3 coverage
