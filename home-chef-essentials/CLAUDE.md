---
title: "Home Chef Essentials — Governance"
content_type: recipe
tags: [governance, rules, cooking, home-chef, techniques, meal-prep]
pinned: true
---

# Home Chef Essentials — Governance

## Purpose

A science-informed knowledge base for the home cook who wants to understand *why* things work, not just follow recipes. Covers technique, flavor building, meal prep, seasonal awareness, sourcing, nutrition, and cuisine fundamentals. Inspired by the approaches of Kenji López-Alt (science-driven), Samin Nosrat (elemental framework), Joshua Weissman (home leveling-up), and Chris Young (precision methods).

## Rules

1. **Explain the why.** Every technique note should explain the science or principle behind it. "Dry your chicken before roasting" → "Surface moisture must evaporate before browning begins (Maillard reaction starts at 280°F/140°C)."
2. **Be practical first.** Theory serves practice. If a shortcut works 95% as well, say so.
3. **Seasonal awareness without guilt.** Guide toward what's in season and why it tastes better, but never shame choices. Frozen vegetables are great. Canned tomatoes are often superior.
4. **Sourcing is education, not fear.** Help people understand labels, grades, and origins so they can make informed choices — not anxious ones.
5. **Allow indulgence.** A good butter sauce, a proper braise, a decadent dessert — these are part of the craft. No apologizing for calories.
6. **Search first.** Before answering from general knowledge, search this vault for relevant technique or guidance notes.
7. **Keep notes focused.** One topic per research note. Under 200 lines.
8. **Attribute approaches.** When referencing a specific chef's method or framework, name them.

## Directory Structure

```
home-chef-essentials/
├── CLAUDE.md                    # This file
├── bootstrap.md                 # Pinned — quick lookup table
├── config.toml.example          # SAME configuration template
├── hub/                         # Category index notes
│   ├── techniques.md
│   ├── meal-prep.md
│   ├── seasonal-produce.md
│   ├── nutrition-basics.md
│   ├── pantry-essentials.md
│   └── sourcing.md
├── research/                    # Detailed guides
│   ├── techniques/              # Science of cooking
│   ├── meal-prep/               # Batch cooking and planning
│   ├── seasonal/                # Produce by season
│   ├── nutrition/               # Balanced eating
│   ├── sourcing/                # Food origins and labels
│   ├── cuisine/                 # Cuisine-specific guides
│   └── equipment/               # Tools and gear
├── entities/                    # Chef/source reference
├── decisions/                   # Your cooking decisions
├── templates/                   # Meal plan templates
└── sessions/                    # Session handoffs
```

## Flavor Framework

When advising on flavor building, reference Samin Nosrat's four elements:
- **Salt** — enhances and balances
- **Fat** — carries flavor, adds richness and texture
- **Acid** — brightens and cuts richness
- **Heat** — transforms texture and develops flavor

## Using SAME Search

```bash
# CLI search
same search "how to get crispy chicken skin"

# MCP search
mcp__same__search_notes(query="braising tough cuts of meat", top_k=5)

# Filtered search
mcp__same__search_notes_filtered(query="what vegetables are in season fall", domain="cooking", top_k=5)
```
