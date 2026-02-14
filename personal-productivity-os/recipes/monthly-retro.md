---
title: "Monthly Retro — Monthly Retrospective"
tags: [recipe, monthly, retro, retrospective, review, patterns]
content_type: recipe
domain: productivity
---

# Monthly Retro — Patterns, Themes, and Course Corrections

## Purpose

Zoom out from weekly tactics to see the month's patterns. Pull data from handoffs and decisions, identify what is working and what is not, and set next month's focus. This is the review cadence where real course corrections happen.

## When to Use

- Last few days of the month or first day of the next
- When the agent detects 4+ weekly reviews have accumulated
- When the user says "let's look at the bigger picture"

## The Script

### Step 1: Gather Monthly Data

Run these searches:

1. `search_notes(query="handoff", top_k=20)` — collect all handoffs from the past month
2. `search_notes(query="decision", top_k=20)` — collect all decisions logged this month
3. Read `current-state/projects.md` — current project statuses
4. Read `current-state/goals.md` — current goal progress
5. `recent_activity(limit=30)` — see the full picture of vault activity

Compile a month-in-review summary covering:
- Number of sessions
- Key decisions made (list them)
- Projects advanced, completed, or stalled
- Goals progressed or unchanged

### Step 2: Present the Month

Share the summary:

> "Here is your month: [N] sessions, [N] decisions made. [Project highlights]. [Goal progress]. The biggest changes were [notable shifts]."

### Step 3: Theme Analysis

Ask:

> "Looking at this month, what patterns do you see? What kept coming up?"

Help surface themes:
- Were they mostly in execution mode or planning mode?
- Did energy levels trend up or down?
- Were there recurring blockers?
- Did any topic dominate unexpectedly?

### Step 4: Start, Stop, Continue

Walk through each:

> "What is one thing you want to START doing next month?"

> "What is one thing you want to STOP doing — or stop tolerating?"

> "What is one thing that is working well and you want to CONTINUE?"

Keep each to one item. Simplicity matters more than completeness.

### Step 5: Update Goals

Read `current-state/goals.md` and review each goal:
- Progress since last month?
- Still relevant and motivating?
- Any that should be adjusted or dropped?

Update the file with `save_note(path="current-state/goals.md", content="...", append=false)`.

If a goal is being dropped, log it: `save_decision(title="Drop [goal]", body="Reason: [why]. Was at [X]% progress. Replaced by / superseded by [what, if anything].", status="accepted")`.

### Step 6: Set Next Month's Focus

Ask:

> "What is the theme or primary focus for next month?"

A theme is a one-phrase intention: "ship the MVP," "rebuild energy," "clear the backlog," "learn and experiment."

Note the theme in `current-state/goals.md`.

### Step 7: Create the Handoff

Call `create_handoff` with:
- **summary**: Monthly retro completed. Key themes: [themes]. Start/stop/continue: [items]. Goals updated.
- **pending**: Next month's focus is [theme]. [Any open items carried forward].
- **blockers**: [Any systemic issues identified].

## What Gets Updated

- `current-state/goals.md` — goal progress updated, new monthly focus set
- `current-state/projects.md` — stale projects flagged or archived
- `decisions/` — any goals dropped or changed logged via `save_decision`
- `sessions/` — retro handoff created via `create_handoff`

## Tips

- This should take 15-20 minutes. It is longer than a weekly review but should not feel heavy.
- If the user has not done weekly reviews, this retro can compensate. Pull from handoffs and decisions instead.
- Do not dwell on missed goals. Focus on what to adjust going forward.
- The start/stop/continue format works because it limits scope. One of each is enough.
- Use `find_similar_notes` on any emerging theme to surface related research.

## See Also

- `research/reviews/monthly-review-framework.md` — The monthly review framework
- `research/reviews/after-action-review.md` — Learning from what happened
- `research/goals/quarterly-planning.md` — The quarterly counterpart
- `hub/reviews.md` — All review cadences in one place
