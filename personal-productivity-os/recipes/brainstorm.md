---
title: "Brainstorm — Structured Idea Generation"
tags: [recipe, brainstorm, ideas, divergent-thinking, creativity]
content_type: recipe
domain: productivity
---

# Brainstorm — Diverge, Cluster, Converge, Commit

## Purpose

Run a structured brainstorming session that goes from wide-open ideation to concrete commitments. Four phases prevent the two most common brainstorm failures: not generating enough ideas (premature convergence) and not acting on any of them (all talk, no commit).

## When to Use

- Facing a problem with no obvious solution
- Starting a new project and exploring approaches
- Feeling creatively stuck or stale
- The user says "I need ideas" or "let's brainstorm"

## The Script

### Step 1: Set the Question

Ask:

> "What is the question or challenge we are brainstorming on? Try to phrase it as a 'How might we...' question."

Help reframe if needed. "I need a better morning routine" becomes "How might we design a morning that sets up the rest of the day?"

The question shapes the output. Spend a minute getting it right.

### Step 2: Search for Existing Context

Run `search_notes(query="[brainstorm topic]", top_k=5)` to see if the vault has relevant research or past decisions. Share briefly:

> "Before we start, there is some context in the vault: [one-sentence summary of relevant notes]. Keep that in the back of your mind."

Do not let research constrain ideation — just prime the pump.

### Phase 1: Diverge (5 minutes)

Set the ground rules:

> "For the next few minutes, quantity over quality. No judgment, no evaluation. Wild ideas are welcome — the crazier the better. We will filter later. Go."

Techniques to keep ideas flowing:
- **What if...?** — "What if money was not an issue? What if you had unlimited time?"
- **Reverse it** — "What would make this problem worse? Now flip each answer."
- **Steal** — "Who does this well? What would they do?"
- **Combine** — "What if you merged idea 3 with idea 7?"

Capture every idea as a short phrase. Aim for 15-20 ideas minimum. If ideas slow down, prompt with one of the techniques above.

### Phase 2: Cluster (3 minutes)

Review the list and group similar ideas:

> "Let's look at these. I see some natural clusters: [group 1 theme], [group 2 theme], [group 3 theme]. Does that grouping make sense, or do you see it differently?"

Name each cluster. Usually 3-5 clusters emerge. Some ideas will not fit a cluster — that is fine, keep them as wildcards.

### Phase 3: Converge (5 minutes)

Now evaluate. For each cluster, ask:

> "Looking at [cluster name], which ideas here have the most potential? Think about impact versus effort."

Use a simple 2x2 to help:
- **High impact, low effort** — Do these first
- **High impact, high effort** — Plan these as projects
- **Low impact, low effort** — Maybe, if time allows
- **Low impact, high effort** — Skip these

Select the top 3 ideas across all clusters.

### Phase 4: Commit (3 minutes)

For each of the top 3 ideas, define the next action:

> "For [idea], what is the single next step to move this forward?"

Each next action should be concrete and doable within a day.

If any idea requires a decision (choosing between approaches, committing resources), log it:

```
save_decision(
  title="[decision from brainstorm]",
  body="During brainstorm on [topic]. Chose [idea] because [reasoning]. Alternatives considered: [other top ideas].",
  status="accepted"
)
```

### Step 3: Capture the Output

Save the brainstorm results if the user wants to keep them. Options:
- Add next actions to `current-state/projects.md`
- Create a new project entry in `entities/projects.md` if an idea became a project
- Save the full brainstorm as a note in an appropriate location

## What Gets Updated

- `decisions/` — any commitments made via `save_decision`
- `current-state/projects.md` — if new next actions were defined
- `entities/projects.md` — if a new project emerged from the brainstorm

## Tips

- The diverge phase should feel slightly uncomfortable. If all ideas are "reasonable," you are converging too early.
- Do not let the user evaluate during Phase 1. If they say "that would never work," redirect: "We will filter later. For now, keep going."
- 15-20 ideas is a minimum. The best ideas usually come after the obvious ones are exhausted.
- If the user is low-energy, shorten to 3 ideas and one commit. A small brainstorm is better than none.

## See Also

- `research/decisions/decision-frameworks.md` — For evaluating brainstorm output
- `research/energy/deep-work-flow.md` — Creative work needs protected time
- `hub/projects.md` — If ideas become projects
