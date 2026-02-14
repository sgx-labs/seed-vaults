---
title: "Fitness & Wellness — Governance"
content_type: recipe
tags: [governance, rules, fitness, wellness, strength, recovery, nutrition]
pinned: true
---

# Fitness & Wellness — Governance

## Purpose

An evidence-based fitness and wellness knowledge base for people who want to train smarter, recover better, and build sustainable habits. Covers strength training, cardiovascular conditioning, mobility, recovery, nutrition for training, habit formation, and common injury prevention.

## IMPORTANT DISCLAIMER

**This vault is for educational and informational purposes only. It is NOT medical advice.**

- Consult a physician before starting any exercise program
- Consult a qualified professional for injuries, pain, or medical conditions
- Stop any exercise that causes sharp pain
- The information here is AI-generated and has not been reviewed by medical professionals
- Individual needs vary — what works for one person may not work for another

## Rules

1. **Safety first.** Every exercise note includes form cues and common mistakes. When in doubt, recommend consulting a professional.
2. **Disclaimers are mandatory.** Every note that touches health, injuries, or medical conditions must include a disclaimer. This is non-negotiable.
3. **Evidence-based.** Reference established exercise science principles. Cite sources where possible (ACSM, NSCA, peer-reviewed research).
4. **Progressive and sustainable.** Recommend gradual progression. No crash programs, extreme protocols, or "no pain no gain" mentality.
5. **Inclusive.** Content should be useful across fitness levels. Provide modifications and progressions.
6. **Recovery is training.** Sleep, rest days, and deload weeks are not optional — they're part of the program.
7. **Search first.** Before answering from general knowledge, search this vault.
8. **Keep notes focused.** One topic per research note. Under 200 lines.
9. **No body shaming.** Frame fitness in terms of capability, health, and enjoyment — never appearance-based shame.

## Directory Structure

```
fitness-and-wellness/
├── CLAUDE.md                    # This file
├── bootstrap.md                 # Pinned — quick lookup table
├── config.toml.example          # SAME configuration template
├── hub/                         # Category index notes
│   ├── strength-training.md
│   ├── cardio-and-conditioning.md
│   ├── mobility-and-recovery.md
│   ├── nutrition-for-training.md
│   ├── habit-building.md
│   └── common-issues.md
├── research/                    # Detailed guides
│   ├── strength/                # Programming and exercises
│   ├── cardio/                  # Cardiovascular fitness
│   ├── mobility/                # Flexibility and movement
│   ├── recovery/                # Rest, sleep, deload
│   ├── nutrition/               # Training nutrition
│   ├── habits/                  # Behavior and consistency
│   └── injuries/                # Prevention and management
├── entities/                    # Source references
├── decisions/                   # Training decisions
├── templates/                   # Program templates
└── sessions/                    # Session handoffs
```

## Safety Protocol

When the agent detects a question about pain, injury, or medical conditions:
1. **Always include a disclaimer** recommending professional consultation
2. Provide general educational information only
3. Never diagnose or prescribe treatment
4. Suggest seeing a doctor, physical therapist, or qualified trainer

## Using SAME Search

```bash
# CLI search
same search "how to fix lower back pain from deadlifts"

# MCP search
mcp__same__search_notes(query="beginner strength training program", top_k=5)

# Filtered search
mcp__same__search_notes_filtered(query="post-workout nutrition protein", domain="fitness", top_k=5)
```
