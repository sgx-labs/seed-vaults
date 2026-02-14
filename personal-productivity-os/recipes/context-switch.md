---
title: "Context Switch — Task Transition Protocol"
tags: [recipe, context-switch, transitions, adhd, focus, task-management]
content_type: recipe
domain: productivity
workstream: adhd
---

# Context Switch — Task Transition Protocol

## Purpose

Help transition between tasks without losing context or momentum. Context switching costs 15-25 minutes of recovery time — this recipe cuts that to under 5.

## When to Use

- Switching between projects
- After an interruption (meeting, message, break)
- When you're done with one task but not sure what's next
- After a long focus session when you need to change gears

## Time: 3-5 minutes

## The Script

### Step 1: Close the Loop (1 minute)
Ask: "Before we switch — what's the state of what you were just working on?"

Capture a quick handoff note using `create_handoff`:
- What was accomplished
- What's pending
- Any blockers

### Step 2: Physical Reset (30 seconds)
Say: "Stand up. Stretch. Take 3 deep breaths. Get water if you need it."

Wait for the user to return. Don't rush this — the physical transition matters.

### Step 3: Load New Context (1 minute)
Ask: "What are you switching to?"

Use `get_session_context` and `search_notes` to surface:
- Recent handoffs for the new task
- Related research notes
- Current state of the project

Present a brief summary: "Here's where you left off on [project]..."

### Step 4: Set Intention (30 seconds)
Ask: "What's the ONE thing you want to accomplish in this next session?"

Write it down. A single clear intention prevents the post-switch wandering that eats time.

### Step 5: Go
Say: "You're loaded up. Let's do this."

## What Gets Updated
- Handoff note created for previous task
- New task context surfaced

## Agent Notes
- Keep this fast. The whole point is speed.
- If the user doesn't want to close the loop on the previous task, skip it — don't force it
- The physical reset is important for ADHD brains. Don't skip it, but don't make it weird either.

## See Also
- research/adhd/transition-rituals.md
- research/energy/context-switching-cost.md
- recipes/morning-start.md
