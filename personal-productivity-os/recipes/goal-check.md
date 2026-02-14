---
title: "Goal Check — Quarterly Goal Review"
tags: [recipe, goals, quarterly, review, alignment, priorities]
content_type: recipe
domain: productivity
---

# Goal Check — Quarterly Goal Review

## Purpose

Review all active goals at the quarter boundary. Assess progress honestly, kill goals that no longer matter, adjust targets, and set priorities for the next quarter. This is the highest-level review cadence — where strategy meets reality.

## When to Use

- End of quarter (last week of March, June, September, December)
- When the user feels misaligned between daily work and bigger ambitions
- After a major life or work change that shifts priorities
- When goals feel stale or disconnected from reality

## The Script

### Step 1: Load Current Goals

Read `current-state/goals.md`. Present each goal with its current status:

> "Here are your active goals for this quarter: [list each with progress percentage or status]. Let's walk through them one at a time."

Also run `search_notes(query="goal quarterly progress", top_k=10)` to find any related decisions or handoffs that mention goal progress.

### Step 2: Review Each Goal

For each goal, ask three questions:

**Progress:**
> "Where are you on this? Rough percentage — are you on track, behind, or ahead?"

**Blockers:**
> "Is anything blocking progress? Resources, decisions, dependencies, energy?"

**Relevance:**
> "Does this goal still matter to you? If you were setting goals today from scratch, would this make the list?"

Update the progress in `current-state/goals.md` as you go.

### Step 3: Kill What Does Not Matter

If a goal is no longer relevant, do not keep it alive out of guilt. Log the decision:

```
save_decision(
  title="Drop goal: [goal name]",
  body="Was at [X]% progress. Dropping because [reason — priorities shifted, no longer relevant, superseded by something else]. Not a failure — it was the right call for that time, but circumstances changed.",
  status="accepted"
)
```

Confirm with the user:

> "I have logged dropping [goal]. It is off your plate. No guilt — priorities change."

### Step 4: Adjust Targets

For goals that are still relevant but off track, ask:

> "Do you want to adjust the target, extend the timeline, or change the approach?"

Options:
- **Adjust**: Lower or raise the bar based on what you learned
- **Extend**: Give it another quarter with a clear plan
- **Restructure**: Same outcome, different path

Log any significant adjustments as decisions.

### Step 5: Set Next Quarter's Priorities

Ask:

> "Looking at the next three months, what are the 2-3 things that would make the biggest difference?"

Reference the quarterly planning research:

Run `search_notes(query="quarterly planning themes", top_k=3)` and surface `research/goals/quarterly-planning.md` if relevant.

Help them set new goals using this structure:
- **Goal**: What does success look like?
- **Measure**: How will you know you achieved it?
- **First action**: What is the first step?
- **Connected project**: Which active project serves this goal?

### Step 6: Align Projects to Goals

Read `current-state/projects.md`. Check that every active project connects to a goal. Flag orphaned projects:

> "I notice [project] is active but does not connect to any of your goals. Is it serving an unstated goal, or should we reconsider it?"

### Step 7: Update and Save

Update `current-state/goals.md` with:
- Progress on continuing goals
- New goals for next quarter
- Dropped goals moved to an archive section
- Next quarter's theme or focus

Use `save_note(path="current-state/goals.md", content="...", append=false)`.

Create a handoff:

```
create_handoff(
  summary="Quarterly goal review completed. [N] goals continuing, [N] dropped, [N] new. Next quarter theme: [theme].",
  pending="First actions for new goals: [list].",
  blockers="[Any systemic blockers identified]."
)
```

## What Gets Updated

- `current-state/goals.md` — full refresh of goals and progress
- `current-state/projects.md` — orphaned projects flagged, alignment checked
- `decisions/` — dropped or adjusted goals logged via `save_decision`
- `sessions/` — quarterly review handoff created via `create_handoff`

## Tips

- This should take 20-30 minutes. Longer than a monthly retro, but not a marathon.
- 3-5 goals per quarter is the sweet spot. More means less focus.
- Include at least one personal/growth goal, not just output goals.
- Dropping a goal is not failure. It is resource allocation.
- If the user has not done monthly retros, this review can absorb that function.

## See Also

- `research/goals/quarterly-planning.md` — The full quarterly planning framework
- `research/goals/okr-framework.md` — OKR structure for goal setting
- `research/goals/goal-setting-pitfalls.md` — Common traps to avoid
- `research/goals/saying-no.md` — The discipline of fewer goals
- `hub/goals.md` — Goal tracking overview
