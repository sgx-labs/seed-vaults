---
title: "Fitness & Wellness Seed Specification"
content_type: recipe
tags: [seed-spec, fitness, wellness, strength-training, recovery, nutrition]
---

# Fitness & Wellness Seed

## Metadata
- **Name**: fitness-and-wellness
- **Type**: Knowledge Seed (pre-filled content)
- **Audience**: Anyone interested in training smarter — from beginners to intermediate lifters
- **Note count target**: 55-65
- **Sources**: ACSM guidelines, NSCA principles, peer-reviewed exercise science

## Purpose
An evidence-based fitness knowledge base emphasizing sustainable training, proper form, progressive overload, recovery, and habit building. Every note includes appropriate disclaimers. The tone is encouraging and practical — fitness is framed as capability and enjoyment, not punishment or appearance-based shame. Modifications are always provided for different fitness levels.

## Hub Categories

1. hub/strength-training.md — Resistance training fundamentals and programming
2. hub/cardio-and-conditioning.md — Cardiovascular fitness and conditioning
3. hub/mobility-and-recovery.md — Flexibility, movement quality, and rest
4. hub/nutrition-for-training.md — Fueling workouts and recovery
5. hub/habit-building.md — Consistency, motivation, and behavior change
6. hub/common-issues.md — Pain, plateaus, and injury prevention

## Key Questions

1. How do I start strength training as a complete beginner?
2. What's the right way to deadlift without hurting my back?
3. How many rest days do I actually need?
4. What should I eat before and after a workout?
5. How do I break through a strength plateau?
6. What does an evidence-based warm-up look like?
7. How do I build a running habit from zero?
8. What mobility work actually matters?
9. How does sleep affect training and recovery?
10. How much protein do I really need?
11. What's the difference between HIIT and steady-state cardio?
12. How do I train around a minor injury?
13. What does progressive overload actually mean in practice?
14. How do I stay consistent when motivation drops?
15. What are the warning signs I'm overtraining?
16. How do I program a training week (strength + cardio + rest)?
17. What supplements are actually worth taking?
18. How do I foam roll effectively?
19. What heart rate zones should I train in?
20. When should I see a doctor vs. push through discomfort?

## Entities

1. entities/acsm.md — American College of Sports Medicine guidelines
2. entities/nsca.md — National Strength and Conditioning Association
3. entities/starting-strength.md — Rippetoe's novice barbell program
4. entities/evidence-based-sources.md — Key researchers and publications

## CLAUDE.md Governance

- Safety disclaimers on every health-related note
- Evidence-based recommendations with source attribution
- Progressive and sustainable — no extreme protocols
- Inclusive — modifications for all levels
- Recovery is part of training, not optional
- No body shaming

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
