---
title: "Weekly Review — 15-Minute Reflection"
tags: [recipe, weekly-review, reflection, planning, review]
content_type: recipe
domain: productivity
---

# Weekly Review — Conversational 15-Minute Check-In

## Purpose

Close out the week and set up the next one. This is a conversation, not a form. Pull real data from the vault so the user does not have to remember anything — then walk through reflection questions that surface what matters.

## When to Use

- End of work week (Friday afternoon or Sunday evening)
- When the agent detects 6-8 days since the last weekly review
- When the user says "let's do a review" or "how did the week go?"

## The Script

### Step 1: Gather the Data

Run these in parallel:
1. `get_session_context` — pull the latest handoff and recent decisions
2. `search_notes(query="handoff", top_k=10)` — find all handoffs from the past week
3. Read `current-state/projects.md` — current project status
4. `recent_activity(limit=15)` — see what notes changed this week

Compile a brief summary of the week's activity: sessions held, decisions made, projects advanced, notes created or updated.

### Step 2: Present the Week

Share the summary conversationally:

> "Here is what happened this week: you had [N] sessions. You made [N] decisions, including [notable ones]. [Project] moved from [status] to [status]. [Any new blockers or completions]."

### Step 3: Reflection — What Went Well?

Ask:

> "What went well this week? What are you proud of, even if it seems small?"

Listen. Acknowledge. Do not rush to the next question.

### Step 4: Reflection — What Drained You?

Ask:

> "What drained your energy this week? Anything that felt harder than it should have been?"

If they mention recurring drains, run `search_notes(query="[drain topic]", top_k=3)` to see if the vault has relevant strategies.

### Step 5: Reflection — One Thing Different

Ask:

> "If you could change one thing about next week, what would it be?"

This is the actionable takeaway. Help them turn it into a concrete adjustment.

### Step 6: Update the Project Tracker

Walk through `current-state/projects.md` together:
- Update statuses for any projects that changed
- Archive completed items (move to a "Completed" section or remove)
- Add any new projects that emerged this week
- Flag stale projects (no activity in 2+ weeks)

Use `save_note(path="current-state/projects.md", content="...", append=false)` to save updates.

### Step 7: Set Next Week's Focus

Ask:

> "What is your theme or top focus for next week?"

This does not need to be elaborate — one sentence is fine. Note it in the project tracker or goals file.

### Step 8: Create the Handoff

Call `create_handoff` with:
- **summary**: Key accomplishments and review highlights
- **pending**: Next week's focus and any open items
- **blockers**: Anything unresolved

## What Gets Updated

- `current-state/projects.md` — project statuses refreshed
- `current-state/goals.md` — if goal progress changed
- `sessions/` — new handoff created
- `decisions/` — if any review-triggered decisions were made

## Tips

- Keep it to 15 minutes. If the user is engaged and wants more, let it flow, but do not force length.
- Frame reflections as genuine curiosity, not a performance review.
- If the user skipped last week's review, do not mention it. Just do this one.
- If energy patterns emerge across weeks, mention them once — do not repeat.

## See Also

- `research/reviews/weekly-review-15min.md` — The 15-minute review framework
- `research/reviews/review-consistency.md` — Making reviews stick
- `research/reviews/journaling-for-clarity.md` — Reflection as a thinking tool
