---
title: "Morning Start — Session Orientation"
tags: [recipe, morning, start, orientation, energy, priorities]
content_type: recipe
domain: productivity
---

# Morning Start — 5-Minute Session Orientation

## Purpose

Get oriented in under five minutes. Load context, surface the top priority, check energy, and set a clear intention for the session. No fumbling, no re-reading old notes, no "where was I?"

## When to Use

- Start of any working session
- After a break of more than a few hours
- When you sit down and do not know what to work on

## The Script

### Step 1: Load Context

Call `get_session_context` to pull pinned notes, the latest handoff, and recent decisions. Read the handoff summary aloud to the user:

> "Last session you [summary]. You still need to [pending]. [Blockers if any]."

### Step 2: Check the Priority Board

Read `current-state/projects.md`. Identify:
- The highest-priority active project
- Any approaching deadlines (within 3 days)
- Any items that have been "waiting on" for more than a week

Surface the top priority:

> "Your main focus right now is [project]. [Deadline context if relevant]."

### Step 3: Energy Check (Opt-In)

Offer a quick energy check — do not ask every session. Once or twice a week is plenty.

> "Want to do a quick energy check? No pressure — I can just jump straight to your priorities."

If the user declines, skip directly to Step 4. If they accept:

> "How are you feeling today — sharp and ready, somewhere in the middle, or running on fumes?"

Adjust recommendations based on the answer:
- **Sharp**: Suggest deep work on the top priority
- **Middle**: Suggest moderate tasks — planning, reviews, collaboration items
- **Low**: Suggest low-friction work — admin, email, organizing, or a shorter session

### Step 4: Surface One Relevant Note

Run `search_notes(query="[top priority topic]", top_k=3)`. Pick the most relevant research note and offer it briefly:

> "There is a note on [topic] that might be useful for what you are working on today. Want me to pull it up?"

Do not dump research. One note, one sentence, one offer.

### Step 5: Set the Intention

Ask the user to name one thing they want to accomplish this session:

> "What is the one thing that, if you finished it today, would make today a win?"

Confirm and move into work mode.

## What Gets Updated

- Nothing during this recipe — it is read-only orientation
- If energy data is notable (pattern emerging), optionally note it in `entities/me.md`

## Tips

- Keep this under 5 minutes. If it takes longer, you are over-explaining.
- Skip the energy check if the user seems eager to dive in.
- If there is no handoff from the previous session, start with `recent_activity(limit=5)` instead.
- On low-energy days, suggest the `unstick-me` recipe if the user seems stuck.

## See Also

- `research/energy/energy-audit.md` — Understanding energy patterns
- `research/adhd/task-initiation.md` — When starting feels impossible
- `research/same-integration/context-surfacing-patterns.md` — How context loading works
