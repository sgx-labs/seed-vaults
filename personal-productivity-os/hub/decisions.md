---
title: "Decisions — Decide Once, Move Forward"
tags: [decisions, frameworks, logging, rationale, prioritization]
content_type: hub
domain: productivity
---

# Decisions — Decide Once, Move Forward

## Overview

Decision making improves when you capture the rationale, not just the outcome. A decision log prevents re-litigation, reduces decision fatigue, and builds a searchable record of trade-offs considered and alternatives rejected. This hub covers decision frameworks like pre-mortems and two-way door analysis for reversible decisions, strategies for overcoming analysis paralysis, and structured approaches for the choices that actually matter. Log once, move forward, and let the agent surface context when it is relevant again.

## Why Log Decisions

1. **Prevents re-litigation**: "We already decided this in October" — with the link
2. **Captures context**: The reasons behind a decision fade faster than the decision itself
3. **Enables pattern recognition**: Looking back at decisions reveals your thinking patterns
4. **Reduces decision fatigue**: Knowing you can look it up frees mental bandwidth

## Decision Log

All decisions are logged in `decisions/log.md`. The agent appends to this file when you make a decision during conversation. Each entry includes:
- Date
- What was decided
- Why (the rationale)
- What was considered and rejected
- Status (active, superseded, revisit-on-date)

Template: `templates/decision-template.md`

## When to Log a Decision

Log any decision where:
- You considered multiple options
- The rationale might not be obvious later
- Others might question why you chose this path
- You might be tempted to revisit it later
- It affects a project timeline, budget, or direction

## Decision Frameworks

### For Quick Decisions
- **Two-door test**: Is this reversible? If yes, decide fast and move on. If not, invest more time.
- **10-10-10**: How will I feel about this in 10 minutes, 10 months, 10 years?

### For Big Decisions
- **Pros/cons with weights**: Not all factors are equal. Weight them.
- **Pre-mortem**: Assume the decision failed. What went wrong? Address those risks.
- **Advice test**: What would I tell a friend to do?

See: `research/decisions/decision-frameworks.md`, `research/decisions/decision-fatigue.md`

## Research Notes

- `research/decisions/decision-frameworks.md` — Frameworks for different decision types
- `research/decisions/decision-fatigue.md` — How to reduce daily decision load
- `research/decisions/context-switching-cost.md` — Decisions about how to spend attention

## See Also

- `hub/thinking.md` — Mental models and cognitive bias awareness for clearer thinking
- `hub/reviews.md` — Reviews surface patterns in your decision-making over time
- `hub/projects.md` — Project decisions benefit from explicit logging
