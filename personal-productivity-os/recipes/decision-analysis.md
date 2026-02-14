---
title: "Decision Analysis — Structured Decision Making"
tags: [recipe, decision, analysis, pre-mortem, framework]
content_type: recipe
domain: productivity
---

# Decision Analysis — Structured Decision Process

## Purpose

Walk through a non-trivial decision using a structured process. Define the decision clearly, generate real options, evaluate against criteria, stress-test with a pre-mortem, commit, and log. Prevents both analysis paralysis and impulsive choices.

## When to Use

- Facing a decision with real consequences and no obvious answer
- Choosing between 2+ options and going in circles
- Feeling pressure to decide but wanting to think it through
- When a past decision comes up for re-evaluation

## Time Cap: 15 Minutes

This recipe has a 15-minute timer. If you don't have a decision by then, either you need more information (defer and gather it) or you're overthinking it (pick the best option and move on). Most decisions are reversible.

## The Script

### Step 1: Define the Decision

Ask:

> "What is the decision you need to make? Try to state it as a clear question."

Help reframe if needed. "What should I do about the project?" becomes "Should I continue, pause, or cancel Project X given the current resource constraints?"

A well-defined decision is half the answer.

### Step 2: Check Reversibility

Ask:

> "If you made this choice and it turned out wrong, could you undo it or change course? How easily?"

If it is a two-way door (easily reversible), suggest deciding quickly:

> "This looks reversible. You could try one option, see how it goes, and switch if it does not work. Want to just pick and move on?"

If it is a one-way door (hard to reverse), proceed with the full analysis.

### Step 3: List Options

Ask:

> "What are your options? Let's get at least three on the table."

Push for a third option if they only see two. Binary framing ("do it or don't") usually hides better alternatives. Common third options:
- Do a smaller version
- Delay and gather more information
- Delegate or partner
- Combine elements of two options

### Step 4: Define Criteria

Ask:

> "What matters most in this decision? What are you optimizing for?"

Help them name 3-5 criteria and rank them by importance. Common criteria: cost, time, risk, learning, alignment with goals, energy required, reversibility.

### Step 5: Pre-Mortem Each Option

> **Skip this step for reversible (two-way door) decisions.** Save pre-mortems for decisions that are hard to undo. If Step 2 identified this as reversible, jump to Step 6 or Step 7.

For each option, ask:

> "Imagine you chose this and six months from now it failed completely. What went wrong?"

Run `search_notes(query="pre-mortem decision analysis", top_k=3)` to surface the pre-mortem framework if available.

Document the failure modes for each option. Look for:
- Which failures are likely vs. unlikely?
- Which are preventable vs. uncontrollable?
- Which have the worst consequences?

### Step 6: Score and Compare

If helpful, create a simple comparison:

| Criterion | Weight | Option A | Option B | Option C |
|-----------|--------|----------|----------|----------|
| [criteria] | [1-5] | [1-5] | [1-5] | [1-5] |

Not every decision needs a matrix. Use it when the user is genuinely torn between close options.

### Step 7: Commit and Log

Ask:

> "Based on all of this, what are you going with?"

Once they decide, log it immediately:

```
save_decision(
  title="[concise decision title]",
  body="Decision: [what was chosen]. Alternatives: [what was rejected and why]. Key criteria: [what mattered most]. Pre-mortem risks: [top risks and mitigations].",
  status="accepted"
)
```

Confirm:

> "Logged. You chose [option] because [rationale]. If this comes up again, we have the reasoning on record."

### Step 8: Define the First Action

Ask:

> "Now that this is decided, what is the first thing you need to do to act on it?"

A decision without a next action is just an opinion.

## What Gets Updated

- `decisions/` — new decision logged via `save_decision`
- `current-state/projects.md` — if the decision affects a project's status or direction
- `entities/projects.md` — if the decision creates, changes, or cancels a project

## Tips

- The pre-mortem is the most valuable step. Do not skip it.
- Most decisions should take 10-15 minutes with this process. If it takes longer, the decision might need more information, not more analysis.
- If the user is agonizing, ask the advice test: "What would you tell a friend in this situation?"
- Reference past decisions: `search_notes(query="[related topic] decision", top_k=5)` to check for precedent.

## Decision Fatigue Guard

If you've already made 3+ decisions today, consider deferring this one to tomorrow. Decision fatigue is real — your judgment degrades with each decision, and a fresh morning brain will make a better call than a depleted afternoon one.

## See Also

- `research/decisions/decision-frameworks.md` — Full framework catalog
- `research/decisions/decision-fatigue.md` — Why decision quality degrades
- `research/thinking/pre-mortem.md` — Deep dive on the pre-mortem technique
- `hub/decisions.md` — Decision tracking and patterns
