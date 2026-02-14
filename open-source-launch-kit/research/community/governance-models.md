---
title: "What Governance Model Should My Project Use?"
tags: [governance, leadership, community, decision-making, bdfl, maintainer-council]
content_type: research
domain: engineering
---

# What Governance Model Should My Project Use?

## The Question

How do you make decisions as your project grows? Who has merge access? How do you handle disagreements?

## Governance Models

### BDFL (Benevolent Dictator for Life)

One person makes all final decisions. Most common for small to medium projects.

**Works when:**
- You are the solo maintainer
- The project has a clear opinionated vision
- Fast decision-making matters more than consensus

**Risks:**
- Bus factor of 1
- Community may feel excluded from decisions
- Burnout from being the single decision-maker

**Examples:** Linux (Linus Torvalds), Python (Guido van Rossum, historically)

### Maintainer Council

A small group (3-5) shares decision-making authority. Decisions by consensus or majority vote.

**Works when:**
- You have 2+ trusted, active maintainers
- The project is large enough that one person cannot review everything
- You want to reduce bus factor

**How to transition from BDFL:**
1. Identify your most active, trusted contributors
2. Grant them commit access incrementally
3. Document the decision-making process in GOVERNANCE.md
4. Start with informal consensus, formalize only if needed

### Foundation/Committee

Formal governance with elected roles, bylaws, and structured decision-making.

**Works when:**
- The project has corporate stakeholders
- Hundreds of contributors
- Legal or financial responsibilities

This is overkill for 99% of OSS projects. Do not start here.

## What to Document

At minimum, create a GOVERNANCE.md covering:

```markdown
# Governance

## Decision Making
[Who decides what, and how]

## Maintainers
[List of current maintainers and their areas]

## Becoming a Maintainer
[Criteria and process for promotion]

## Conflict Resolution
[How disagreements are handled]
```

## Practical Tips

- **Start as BDFL.** Do not over-engineer governance before you need it.
- **Write it down when you add your second maintainer.** That is when governance starts to matter.
- **Give merge access gradually.** Start with specific directories or types of changes.
- **Trust but verify.** All commits should go through PRs, even from maintainers.
- **Say no kindly.** You will reject good contributions that do not fit the vision. That is your job.

## When Governance Goes Wrong

- **No governance at all** — Leads to unclear expectations and contributor frustration
- **Too much governance** — Bureaucracy kills small projects faster than bad code
- **Governance without enforcement** — Rules nobody follows are worse than no rules

## Related

- `hub/community.md` — Community building overview
- `research/sustainability/preventing-burnout.md` — Sharing the load prevents burnout
- `hub/sustainability.md` — Long-term project health
