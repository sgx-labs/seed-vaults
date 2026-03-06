---
title: "Style Guide Adoption — Decision Template"
tags: [decision, style-guide, team, adoption, template, documentation]
content_type: decision
domain: technical-writing
---

# Style Guide Adoption

Use this template when deciding whether and how to adopt a documentation style guide.

## DD-XXX: Adopt Documentation Style Guide

**Status:** Proposed
**Date:** YYYY-MM-DD
**Deciders:** [Names]

## Context

[Describe the current situation: inconsistencies in docs, team size, documentation volume, existing conventions (formal or informal).]

## Questions to Answer

1. **Who writes documentation?** Just developers? Technical writers? Both?
2. **How much documentation exists?** A few READMEs or 100+ pages?
3. **What inconsistencies cause the most confusion?** Terminology? Structure? Tone?
4. **How much enforcement is acceptable?** Suggestions only? CI checks? Blocking?
5. **Is there an existing company style guide?** Adapt or create from scratch?

## Options

### Option A: Start from Scratch (5-10 custom rules)

**Effort:** Low (1-2 hours to draft, 1 hour team review)
**Pros:** Perfectly tailored to your team's needs
**Cons:** Requires ongoing maintenance, might miss best practices

### Option B: Adopt an Existing Guide (Microsoft, Google)

**Effort:** Medium (1 hour to review, 1 hour to customize)
**Pros:** Comprehensive, battle-tested, maintained by others
**Cons:** May include rules irrelevant to your context, feels external

### Option C: Minimal Rules + Automated Linting

**Effort:** Medium (2-3 hours setup)
**Pros:** Consistent enforcement, low cognitive load
**Cons:** Automation can feel annoying, doesn't cover everything

### Option D: No Style Guide

**Effort:** Zero
**Pros:** No friction added
**Cons:** Inconsistencies grow with team and doc size

## Decision

[Which option was chosen and the primary reason.]

## Initial Rules (if adopting)

List the first 5-10 rules the team agreed on:

1. [Rule]
2. [Rule]
3. [Rule]
4. [Rule]
5. [Rule]

## Enforcement Plan

| Phase | Timeline | Enforcement Level |
|-------|----------|------------------|
| 1. Draft and agree | Week 1 | None (just agree on rules) |
| 2. Voluntary adoption | Weeks 2-4 | Suggestions in PR reviews |
| 3. CI warnings | Month 2 | Automated warnings, not blocking |
| 4. CI enforcement | Month 3+ | Blocking on new docs only |

## Review Date

[When will the style guide be reviewed? 6 months is typical for initial adoption.]

## Reference

- See `hub/writing-style-guide.md` for style principles
- See `guides/creating-a-style-guide.md` for step-by-step setup
- See `entities/writing-tools-reference.md` for Vale and other tools
