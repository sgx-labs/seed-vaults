---
title: "Recipes — Session Scripts for Getting Things Done"
tags: [hub, recipes, scripts, workflows, agent]
content_type: hub
domain: productivity
---

# Recipes — Session Scripts for Getting Things Done

## How Recipes Work

Recipes are not reading material. They are structured scripts that the agent follows step by step to accomplish a specific outcome in a single session. Each recipe tells the agent what to do, what to ask, which SAME tools to call, and what to update in the vault.

Think of a recipe as a protocol: the agent reads it, executes it, and the user gets the benefit without needing to know the underlying process. The user just says "let's do a weekly review" and the agent runs the script.

**Key characteristics of recipes:**
- Written FOR the agent, not for the user to read
- Each has a clear trigger ("when to use")
- Steps reference SAME MCP tools by name (`search_notes`, `save_decision`, `create_handoff`, etc.)
- Each recipe specifies which vault files get updated
- Designed to complete in a single session (5-30 minutes depending on the recipe)

## Recipe Index

### Daily Cadence

| Recipe | Time | When to Use |
|--------|------|-------------|
| [Morning Start](../recipes/morning-start.md) | 5 min | Start of any session. Loads context, checks priorities, sets intention. |
| [End of Day](../recipes/end-of-day.md) | 5 min | End of a session. Captures accomplishments, open threads, creates handoff. |

### When You Need Help

| Recipe | Time | When to Use |
|--------|------|-------------|
| [Unstick Me](../recipes/unstick-me.md) | 3-5 min | You know what to do but cannot start. Diagnoses the blocker and gets you moving. |
| [Brainstorm](../recipes/brainstorm.md) | 15 min | You need ideas. Four-phase process: diverge, cluster, converge, commit. |
| [Decision Analysis](../recipes/decision-analysis.md) | 10-15 min | Facing a real decision with no obvious answer. Structured evaluation with pre-mortem. |
| [Decompose Project](../recipes/decompose-project.md) | 15 min | A project feels vague or overwhelming. Breaks it into milestones and next actions. |
| [Context Switch](../recipes/context-switch.md) | 3-5 min | Transitioning between tasks. Saves state, clears mental cache, orients to the next thing. |
| [Body Double Session](../recipes/body-double-session.md) | 25-50 min | Need focused work with agent support. The agent holds context while you execute. |
| [Discovery Mode](../recipes/discovery-mode.md) | 15-30 min | Explore a topic you do not know yet. Guided research with vault integration. |

### Review Cadences

| Recipe | Time | When to Use |
|--------|------|-------------|
| [Weekly Review](../recipes/weekly-review.md) | 15 min | End of week. Conversational reflection, project updates, next week's focus. |
| [Monthly Retro](../recipes/monthly-retro.md) | 15-20 min | End of month. Pattern analysis, start/stop/continue, goal check. |
| [Goal Check](../recipes/goal-check.md) | 20-30 min | End of quarter. Full goal review, kill/adjust/add goals, set quarterly priorities. |

### Self-Knowledge

| Recipe | Time | When to Use |
|--------|------|-------------|
| [Energy Audit](../recipes/energy-audit.md) | 10-15 min | Map energy patterns across your week. Schedule work by capacity, not availability. |

## How to Use Recipes

**As a user:** Just tell the agent what you need. Say "let's do a weekly review" or "I am stuck" or "help me think through a decision." The agent will find and run the right recipe.

**As an agent:** When the user's request matches a recipe trigger, read the recipe file and follow the script step by step. Adapt to the user's energy and engagement — if they want to skip a step or go deeper on one, follow their lead. The recipe is a guide, not a straitjacket.

## Recipe Design Principles

1. **Teach by doing.** The user learns the process by experiencing it, not by reading about it.
2. **Data before questions.** Pull from the vault first so the user does not have to remember things.
3. **Respect time estimates.** If a recipe says 5 minutes, do not let it sprawl to 20.
4. **Always leave an artifact.** Every recipe should update at least one vault file or create a handoff.
5. **Suggest, do not prescribe.** Offer the next recipe when relevant. Never force a workflow.

## See Also

- `hub/reviews.md` — Review frameworks and cadences
- `hub/systems.md` — Productivity systems and approaches
- `hub/decisions.md` — Decision tracking and patterns
- `hub/energy.md` — Energy management overview
- `hub/projects.md` — Project lifecycle and tracking
