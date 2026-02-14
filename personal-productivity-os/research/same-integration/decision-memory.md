---
title: "Decision Memory — Decide Once, Move On"
tags: [same, integration, decisions, memory, re-litigation, adhd]
content_type: research
domain: productivity
workstream: same-integration
---

# Decision Memory — Decide Once, Move On

## The Problem

You spent 45 minutes weighing the options. You considered the trade-offs. You made the call. Three weeks later, you are having the same debate with yourself. Should you really use that approach? Maybe the other option was better. What if you missed something?

This is re-litigation, and it is one of the most expensive patterns in knowledge work. It consumes time, energy, and confidence. It is especially common in ADHD brains, where working memory limitations make it hard to hold the full reasoning chain that led to the original decision.

The decision was good. You just forgot why.

## The Cost of Re-Litigation

### Time Cost
Re-opening a settled decision takes 30 to 60 minutes — sometimes longer than the original decision. Multiply this by the dozens of decisions a knowledge worker makes per month. Hours evaporate into circular reasoning.

### Energy Cost
Decisions are cognitively expensive. Re-making a decision you already made spends decision-making energy on zero-value work. See `research/energy/decision-fatigue.md` for why this matters.

### Confidence Cost
Every time you second-guess a past decision, you erode trust in your own judgment. Over time, this makes new decisions harder: "What if I change my mind about this too?" Decisiveness atrophies.

### Progress Cost
While you are re-litigating, you are not moving forward. Projects stall. Deadlines slip. The opportunity cost is invisible but enormous.

## How SAME Solves This

### Capture: `save_decision`

When you make a decision, the agent logs it using `save_decision`:

```
Title: Use weekly sprint cycles for Project Alpha
Status: accepted
Body:
  Decided to use weekly sprints instead of 2-week cycles.
  Reasoning: The project scope is still unclear, and weekly
  cycles let us adjust direction faster.
  Rejected: 2-week sprints (too much planning overhead for
  a team of 2), kanban (no timeboxing = scope creep risk).
```

This takes 30 seconds. The decision, the reasoning, and the rejected alternatives are all captured.

### Retrieval: Automatic Surfacing

Three weeks later, you are thinking about Project Alpha's workflow. You mention it in conversation. The `UserPromptSubmit` hook searches the vault and surfaces the decision note. The agent says:

"You decided to use weekly sprints on January 15th. The reasoning was that weekly cycles allow faster direction changes given the unclear scope. You rejected 2-week sprints due to planning overhead and kanban due to scope creep risk."

The re-litigation impulse dies immediately. The reasoning is right there. You can move on.

### Review: `search_notes`

Want to review all decisions for a project? Search for them:

```
search_notes(query="Project Alpha decisions", top_k=10)
```

Every decision surfaces with full context. This is especially powerful during retrospectives and planning sessions.

## The Highest-ROI Productivity Habit

Decision logging has the highest return on investment of any productivity habit because:

1. **Effort is minimal** — 30 seconds to log a decision during or after the conversation
2. **Savings are recurring** — Every future encounter with that topic saves 30-60 minutes
3. **Confidence compounds** — A record of good decisions builds self-trust
4. **Teams benefit** — Logged decisions survive personnel changes and memory lapses

## What to Log

Not every decision is worth logging. Focus on decisions that:

- Took more than 10 minutes to make
- Involved trade-offs between reasonable alternatives
- Affect how you work for weeks or months
- Are likely to be questioned later (by you or others)
- Represent a change from a previous approach

## What NOT to Log

- Trivial decisions (what to name a variable, which restaurant for lunch)
- Decisions that are easily reversible with no cost
- Preferences that might change naturally over time

## The Decision Review Pattern

Once a month, scan your decision log:

1. **Still valid?** Circumstances change. A decision made with old information may need revisiting — deliberately, not through anxious re-litigation.
2. **Patterns?** Do you see recurring themes? Are certain types of decisions always right? Always wrong? This is meta-learning.
3. **Expiring?** Some decisions have natural expiry dates. "We will revisit pricing after launch." If the condition has been met, schedule the review.

The agent can support this through `search_notes` with time-based queries and through proactive patterns described in `research/same-integration/proactive-agent-patterns.md`.

## The Emotional Component

Re-litigation is often driven by anxiety, not logic. The decision was sound, but the uncertainty feels intolerable. Logging decisions with clear reasoning addresses the anxiety directly: "I am not sure this was right" can be answered with "here is exactly why you decided this, and here is what you considered."

This is not just a productivity tool. It is an anxiety management tool.

## See Also

- `research/decisions/decision-fatigue.md` — The energy cost of decisions
- `research/decisions/decision-frameworks.md` — How to make better decisions
- `research/same-integration/same-as-executive-function.md` — Decision memory in context
- `decisions/log.md` — The active decision log
