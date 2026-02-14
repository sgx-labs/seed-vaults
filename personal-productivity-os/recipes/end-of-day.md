---
title: "End of Day — Session Closeout"
tags: [recipe, end-of-day, closeout, handoff, reflection]
content_type: recipe
domain: productivity
---

# End of Day — 5-Minute Session Closer

## Purpose

Close the loop on the current session. Capture what was done, what is still open, and what tomorrow's top priorities are. This ensures the next session starts clean with full context.

## When to Use

- End of a working day
- Wrapping up a long session
- Before stepping away for more than a few hours
- When the user says "I think I am done for today"

## The Script

### Step 1: Review What Was Accomplished

Scan the current session's conversation. Summarize what was done:

> "Here is what we covered today: [list of accomplishments, decisions, updates made]."

If the session was light, keep this brief. If it was substantial, highlight the top 2-3 wins.

### Step 2: Capture Open Threads

Ask:

> "Is there anything still in progress or on your mind that we should capture before you go?"

Listen for:
- Tasks started but not finished
- Ideas mentioned but not acted on
- Questions that came up but were not answered
- Follow-ups needed from other people

### Step 3: Name Tomorrow's Top 3

Ask:

> "If you could only do three things tomorrow, what would they be?"

If the user is unsure, suggest based on `current-state/projects.md`:

> "Based on your project tracker, these seem like the highest-priority items: [list]. Sound right?"

### Step 4: Update the Project Tracker

Check if any project statuses changed during this session. If so, update `current-state/projects.md` with:
- New statuses
- Completed items moved to done
- New blockers identified
- Next actions updated

Use `save_note(path="current-state/projects.md", content="...", append=false)`.

### Step 5: Log Unlogged Decisions

Scan the session for any decisions made that were not logged. Common ones that get missed:
- "Let's go with option A" — that is a decision
- "I will skip that for now" — that is a decision to defer
- "I changed my mind about X" — that is a decision reversal

For each, call `save_decision(title="...", body="...", status="accepted")`.

### Step 6: Create the Handoff

Call `create_handoff` with:
- **summary**: What was accomplished this session (2-3 sentences)
- **pending**: Open threads and tomorrow's top 3
- **blockers**: Anything that could prevent progress

Confirm:

> "Handoff created. Next session will pick up right where we left off."

### Step 7: Optional Energy Reflection

If it feels natural (not every session), ask:

> "How was your energy today? Anything notable about when you felt sharp or drained?"

If they share something useful, consider updating `entities/me.md` with the pattern. Do not force this step.

## What Gets Updated

- `current-state/projects.md` — statuses refreshed
- `decisions/` — any unlogged decisions captured via `save_decision`
- `sessions/` — new handoff created via `create_handoff`
- `entities/me.md` — only if energy data is notable and worth recording

## Tips

- Keep this under 5 minutes. It should feel like a natural wind-down, not a chore.
- If the user is clearly tired, cut to the essentials: handoff and tomorrow's top priority. Skip the rest.
- The handoff is the one non-negotiable step. Everything else can be abbreviated.
- Do not make energy reflections feel like a survey. Ask only when it is organic.

## See Also

- `research/reviews/journaling-for-clarity.md` — Reflection as closure
- `research/adhd/transition-rituals.md` — Why clean endings matter
- `research/same-integration/context-surfacing-patterns.md` — How handoffs enable continuity
