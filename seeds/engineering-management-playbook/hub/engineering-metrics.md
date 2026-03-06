---
title: "Engineering Metrics That Matter"
tags: [metrics, DORA, cycle-time, velocity, measurement, data-driven, productivity]
content_type: hub
domain: engineering-management
---

# Engineering Metrics That Matter

## Overview

Measurement is powerful and dangerous. The right metrics illuminate problems and guide decisions. The wrong metrics create perverse incentives, gaming, and demoralized teams. This note covers what to measure, what NOT to measure, and how to use metrics without weaponizing them.

## DORA Metrics — The Gold Standard

The four DORA (DevOps Research and Assessment) metrics are the best-validated measures of engineering team effectiveness:

### 1. Deployment Frequency
How often you deploy to production.

| Level | Frequency |
|-------|-----------|
| Elite | Multiple times per day |
| High | Weekly to monthly |
| Medium | Monthly to every 6 months |
| Low | Less than every 6 months |

### 2. Lead Time for Changes
Time from code commit to running in production.

| Level | Lead Time |
|-------|-----------|
| Elite | Less than 1 hour |
| High | 1 day to 1 week |
| Medium | 1 week to 1 month |
| Low | More than 1 month |

### 3. Change Failure Rate
Percentage of deployments that cause a failure in production.

| Level | Failure Rate |
|-------|-------------|
| Elite | 0-15% |
| High | 16-30% |
| Medium | 16-30% |
| Low | 46-60% |

### 4. Mean Time to Recovery (MTTR)
How quickly you recover from a production failure.

| Level | MTTR |
|-------|------|
| Elite | Less than 1 hour |
| High | Less than 1 day |
| Medium | 1 day to 1 week |
| Low | More than 1 week |

**Key insight**: Speed and stability are NOT tradeoffs. Elite teams are faster AND more stable. Fast deployment with good recovery beats slow deployment with fewer failures.

## What NOT to Measure

### Lines of Code
More code is not better code. Measuring LOC incentivizes verbose, unmaintainable solutions.

### Story Points Completed
Velocity is a planning tool, not a performance metric. The moment you use it for evaluation, teams inflate estimates.

### Number of PRs
Incentivizes small, meaningless PRs. Quantity is not quality.

### Hours Worked
Measures presence, not impact. Creates a culture of performative busyness.

### Individual Metrics for Team Outcomes
"Alex closed 47 tickets this sprint" says nothing about whether those tickets mattered, were done well, or helped the team.

## Metrics That Help (Beyond DORA)

- **Cycle time**: Time from "work started" to "deployed." Reveals process bottlenecks.
- **PR review time**: How long PRs sit before review. Long times signal capacity or prioritization problems.
- **On-call pages per rotation**: Rising pages mean growing tech debt or architectural problems.
- **Time spent on unplanned work**: If >30% of capacity goes to firefighting, your planning process needs work.
- **New hire time-to-first-PR**: Measures onboarding effectiveness.

## When NOT to Use This Framework

- **To evaluate individual performance**: Metrics are for teams and systems, not individuals. If you put DORA metrics in someone's performance review, you've misunderstood the framework.
- **Without context**: Numbers without narrative are dangerous. Cycle time went up? Maybe you shipped a complex feature. Maybe your process is broken. The number prompts the question; it doesn't answer it.
- **As targets**: Goodhart's Law: "When a measure becomes a target, it ceases to be a good measure." Track metrics to understand, not to gamify.

## In Reality

Your leadership will ask for "engineering productivity metrics." They usually want reassurance that the team is working hard. What they actually need is confidence that the team is delivering value. Reframe the conversation around DORA and business outcomes.

If someone asks "why can't we measure individual developer productivity?" -- the honest answer is that we can't, reliably. Any proxy metric can be gamed, and the gaming makes everything worse.

## See Also

- `entities/dora-metrics-reference.md` -- DORA benchmarks and definitions
- `hub/team-health-indicators.md` -- Qualitative health signals alongside quantitative
- `hub/sprint-retrospectives.md` -- Where metrics become conversation
- `hub/incident-management.md` -- MTTR tracking
- `hub/meeting-management.md` -- Meeting load as a health metric
