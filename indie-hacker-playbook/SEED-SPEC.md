---
title: "Indie Hacker Playbook Seed — Specification"
content_type: hub
tags: [seed-spec, indie-hacker, playbook, solo-founder, bootstrapping]
---

# Indie Hacker Playbook Seed

## Metadata
- **Name**: indie-hacker-playbook
- **Type**: Hybrid (curated knowledge + templates to fill)
- **Audience**: Solo founders, side project builders, indie hackers, bootstrappers
- **Note count target**: 50-70
- **Agent build time**: 2-3 hours

## Purpose
The complete playbook for building and launching a product as a solo founder. From idea validation to first revenue. Your agent knows the indie hacker patterns so you can move fast and avoid common mistakes.

## Hub Categories

1. hub/validation.md — Idea validation, market research, competitor analysis
2. hub/building.md — MVP strategy, tech stack decisions, speed over perfection
3. hub/launching.md — Launch channels, timing, content, communities
4. hub/pricing.md — Pricing strategy, tiers, free vs paid, conversion
5. hub/growth.md — Distribution, SEO, content marketing, community building
6. hub/operations.md — Solo founder ops, tools, automation, avoiding burnout

## Key Questions

1. How do I validate an idea before building anything?
2. What's the fastest path to MVP?
3. How do I choose a tech stack for a solo project?
4. What's the best way to do a Product Hunt launch?
5. How do I price my product (free tier, pricing page, anchoring)?
6. What distribution channels work for indie products?
7. How do I write landing page copy that converts?
8. How do I set up Stripe/Lemon Squeezy for payments?
9. What legal basics do I need (LLC, terms, privacy policy)?
10. How do I get my first 10 paying customers?
11. How do I build in public effectively?
12. What are the common mistakes that kill indie projects?
13. How do I manage my time as a solo founder with a day job?
14. How do I write a Show HN post that doesn't flop?
15. What metrics matter for an early-stage product?
16. How do I handle support as a solo founder?
17. When do I know if I should pivot or persevere?
18. How do I set up basic analytics without overcomplicating?
19. What's the playbook for getting to $1K MRR?
20. How do I prevent burnout while building alone?

## Entities

1. entities/product-hunt.md — Launch strategy, timing, best practices
2. entities/stripe.md — Setup, pricing models, tax handling
3. entities/indie-hackers.md — Community, posting strategy, audience
4. entities/show-hn.md — Rules, timing, what works, what fails

## Decisions to Pre-Populate

1. AD-001: Ship fast, iterate based on feedback — don't build for 6 months in silence
2. AD-002: Pick boring tech — reliability over novelty for solo projects
3. AD-003: Build distribution before product — audience first, then build what they need
4. AD-004: Charge from day one — free users don't validate willingness to pay

## CLAUDE.md Governance

- This is a playbook — use it as a reference, adapt to your situation
- When making a project decision, log it in decisions/
- Research notes are patterns from successful indie hackers — not universal rules
- Update entity files as platforms change their rules and algorithms

## Config Notes

```toml
[vault]
  path = "."
  skip_dirs = ["_Raw"]
  handoff_dir = "sessions"
  decision_log = "decisions/log.md"

[memory]
  max_token_budget = 1200
  max_results = 4
  composite_threshold = 0.65
```
