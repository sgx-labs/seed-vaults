---
title: "Home Chef Essentials Seed Specification"
content_type: recipe
tags: [seed-spec, cooking, home-chef, techniques, meal-prep, nutrition]
---

# Home Chef Essentials Seed

## Metadata
- **Name**: home-chef-essentials
- **Type**: Knowledge Seed (pre-filled content)
- **Audience**: Home cooks who want to level up — from confident beginners to enthusiastic intermediates
- **Note count target**: 55-65
- **Inspirations**: Kenji López-Alt, Samin Nosrat, Joshua Weissman, Chris Young

## Purpose
A comprehensive home cooking knowledge base that treats cooking as a craft worth understanding. Covers the science of heat and flavor, practical meal prep systems, seasonal produce awareness, informed food sourcing, nutrition fundamentals, cuisine-specific technique, and essential equipment care. The tone is encouraging and practical — indulgence is welcome, shortcuts are respected when they work, and the "why" behind every technique is explained.

## Hub Categories

1. hub/techniques.md — Core cooking techniques index
2. hub/meal-prep.md — Batch cooking and meal planning
3. hub/seasonal-produce.md — What's in season and why it matters
4. hub/nutrition-basics.md — Balanced eating without obsession
5. hub/pantry-essentials.md — Stocking your kitchen intelligently
6. hub/sourcing.md — Being an educated food consumer

## Key Questions

1. Why does my steak never get a good sear?
2. How do I build flavor in a dish that tastes flat?
3. What's the science behind emulsions and why do my sauces break?
4. How do I meal prep for the week without eating sad food?
5. What's in season right now and what should I buy?
6. What does "organic" / "free-range" / "grass-fed" actually mean?
7. How do I braise tough cuts of meat until they're fall-apart tender?
8. What's the best way to store prepped food and how long does it keep?
9. How do I build a well-stocked pantry without spending a fortune?
10. What are the essential knife skills every home cook needs?
11. How does salt actually work in cooking — when and how much?
12. What's the difference between types of cooking oils and when to use each?
13. How do I make restaurant-quality stir-fry at home?
14. What's the science behind baking — flour, gluten, leavening?
15. How do I properly season and care for cast iron?
16. What's the framework for building balanced meals?
17. How do I read nutrition labels and what actually matters?
18. What makes a good stock/broth and why does it matter?
19. How do I adapt recipes for dietary restrictions without ruining them?
20. What are the mother sauces and how do they branch out?

## Entities

1. entities/kenji-lopez-alt.md — The Food Lab, science-driven approach, Serious Eats
2. entities/samin-nosrat.md — Salt Fat Acid Heat, intuitive cooking framework
3. entities/joshua-weissman.md — Budget-friendly, home chef leveling up
4. entities/chris-young.md — Modernist Cuisine, precision cooking

## CLAUDE.md Governance

- Explain the science behind techniques
- Be practical — theory serves the cook
- Seasonal awareness without shame
- Sourcing education without fear
- Allow indulgence — it's part of the craft
- Attribute specific chef approaches

## Config Notes

```toml
[vault]
  path = "."
  skip_dirs = ["_Raw", "templates"]
  handoff_dir = "sessions"
  decision_log = "decisions/log.md"

[memory]
  max_token_budget = 1600
  max_results = 5
  composite_threshold = 0.60
```
