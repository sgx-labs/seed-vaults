---
title: "Bootstrap — Home Chef Quick Reference"
tags: [bootstrap, onboarding, session-start, cooking, techniques, quick-reference]
content_type: hub
domain: cooking
pinned: true
---

# Bootstrap — Home Chef Quick Reference

## Overview

Your home cooking knowledge base — technique, flavor, meal prep, seasonal awareness, and sourcing. This vault draws on the approaches of Kenji López-Alt (science-driven cooking), Samin Nosrat (Salt Fat Acid Heat framework), Joshua Weissman (home chef leveling up), and Chris Young (precision methods). Start here to find what you need fast.

## QUICK LOOKUP TABLE

**What are you trying to do?**

| Goal | Start Here | Key Notes |
|------|-----------|-----------|
| Get a better sear/crust | `research/techniques/science-of-heat-transfer.md` | Dry surface, high heat, don't crowd |
| Fix a flat-tasting dish | `research/techniques/salt-fat-acid-heat-framework.md` | Usually needs acid or salt |
| Make a pan sauce | `research/techniques/emulsions-and-sauces.md` | Deglaze, reduce, mount with butter |
| Braise something tender | `research/techniques/braising-and-slow-cooking.md` | Low temp, time, collagen → gelatin |
| Meal prep the week | `research/meal-prep/batch-cooking-strategy.md` | Components > complete meals |
| Store food safely | `research/meal-prep/food-storage-safety.md` | Temps, times, container types |
| Know what's in season | `research/seasonal/` | Spring, summer, fall, winter guides |
| Understand food labels | `research/sourcing/understanding-food-labels.md` | What organic/free-range actually mean |
| Level up stir-fry | `research/cuisine/asian-stir-fry-and-noodles.md` | Wok hei, mise en place, sauce ratios |
| Build Italian flavors | `research/cuisine/italian-fundamentals.md` | Soffritto, pasta water, simplicity |
| Learn mother sauces | `research/cuisine/french-mother-sauces.md` | Five foundations, infinite derivatives |
| Balance a meal | `research/nutrition/building-balanced-meals.md` | Protein + fiber + fat + color |
| Care for cast iron | `research/equipment/cast-iron-care.md` | Season, use, maintain |

## The Four Elements of Good Cooking

*From Samin Nosrat's Salt Fat Acid Heat:*

| Element | Role | When It's Missing |
|---------|------|-------------------|
| **Salt** | Enhances flavor, balances sweetness | Tastes flat, one-dimensional |
| **Fat** | Carries flavor, adds richness/texture | Tastes thin, unsatisfying |
| **Acid** | Brightens, cuts richness | Tastes heavy, dull |
| **Heat** | Transforms texture, develops flavor | Tastes raw, undeveloped |

If a dish doesn't taste right, it usually needs one of these four adjusted.

## Where To Find Things

| Need | Search for | Or read directly |
|------|-----------|-----------------|
| Cooking science | `heat transfer maillard browning` | `hub/techniques.md` |
| Meal planning | `batch cook weekly prep freezer` | `hub/meal-prep.md` |
| Seasonal produce | `in season spring summer fall winter` | `hub/seasonal-produce.md` |
| Nutrition basics | `macros balanced meals protein` | `hub/nutrition-basics.md` |
| Pantry building | `staples oils vinegar spices` | `hub/pantry-essentials.md` |
| Food sourcing | `organic labels farmers market` | `hub/sourcing.md` |
| Chef approaches | `kenji samin weissman` | `entities/` |

## How To Use This Vault

```bash
# Search for technique help
same search "why does my sauce keep breaking"

# Via MCP while cooking
mcp__same__search_notes(query="internal temperature for chicken", top_k=3)

# Find related techniques
mcp__same__find_similar_notes(path="research/techniques/braising-and-slow-cooking.md", top_k=3)
```

## Internal Temperature Quick Reference

| Protein | Target Temp | Notes |
|---------|-------------|-------|
| Chicken breast | 150°F / 66°C | Juicy at 150°F if held 3 min (USDA allows time-temp combos) |
| Chicken thigh | 175-185°F / 79-85°C | Higher for rendered fat and tender texture |
| Beef (medium-rare) | 130°F / 54°C | Rest 5-10 min, carry-over adds 5-10°F |
| Pork chop | 140°F / 60°C | USDA updated from 160°F in 2011 |
| Fish (salmon) | 120-125°F / 49-52°C | Translucent center, flakes easily |
| Ground beef | 160°F / 71°C | No time-temp alternative for ground meat |

*Always verify with a thermometer. Carry-over cooking raises temp 5-10°F after resting.*
