---
title: "Unstick Me — Task Initiation Protocol"
tags: [recipe, unstuck, task-initiation, adhd, activation-energy, blockers]
content_type: recipe
domain: productivity
workstream: adhd
---

# Unstick Me — Task Initiation Protocol

## Purpose

When you know what to do but cannot start, this recipe diagnoses the blocker and applies the right strategy. It does not lecture — it gets you moving within minutes.

## When to Use

- "I know I should be working on X but I just cannot start"
- Sitting in front of a task for more than 15 minutes without progress
- Feeling paralyzed, overwhelmed, or avoidant
- When the user explicitly asks for help getting started

## The Script

### Step 1: Name the Task

Ask:

> "What is the thing you are stuck on? Just the name or a one-sentence description."

Do not ask for details yet. Just get the task on the table.

### Step 2: Find the Real Blocker

Ask one of these — pick the one that feels right for the moment:

> "When you imagine sitting down to do this, what is the first feeling that comes up?"

Or:

> "Is this stuck because it is unclear, overwhelming, boring, or something else?"

Listen for the blocker type:
- **Unclear**: They do not know what the first step actually is
- **Overwhelming**: The task feels too big or complex
- **Boring**: No dopamine, no motivation, they would rather do anything else
- **Emotional**: Anxiety, fear of failure, perfectionism, or past negative associations
- **Energy**: They are simply depleted right now

### Step 3: Apply the Right Strategy

Based on the blocker type:

**If unclear:**
> "Let me help you find the first step. What does 'done' look like for this task?"
Break it down until the next action is a single, concrete, physical step. Use the decompose-project recipe if the task is actually a project.

**If overwhelming:**
> "Let's shrink this. What is the smallest possible piece you could finish in under 5 minutes?"
The goal is a two-minute start. Not the whole task — just motion.

**If boring:**
> "What could you pair with this to make it less painful? Music, a coffee shop, a timer to race?"
Suggest dopamine stacking strategies. Offer to body-double: "I will stay right here while you work on it — just tell me when you start and I will check in after 10 minutes."

**If emotional:**
> "That makes sense. What is the worst thing that could happen if you did start? And what is the most likely thing?"
Help separate the fear from the reality. Do not dismiss the feeling — validate it, then gently test it.

**If energy:**
> "Sounds like now might not be the time for this specific task. Is there a lower-energy version you could do instead, or should we pick something else entirely?"
Suggest the smallest viable action or redirect to an easier task.

### Step 4: Reference the Research

Run `search_notes(query="task initiation activation energy", top_k=3)`. If `research/adhd/task-initiation.md` comes up, offer it:

> "There is a note on why starting is hard and what actually works. Want the short version?"

Only share if the user seems receptive. Do not lecture.

### Step 5: Set the Tiny Next Action

Confirm one concrete action:

> "So the move right now is: [specific action]. Just that. Nothing more. Ready?"

If they say yes, step back and let them work. Check in after a few minutes if appropriate.

## What Gets Updated

- Nothing by default — this is an in-the-moment intervention
- If a pattern emerges (same task stuck repeatedly), note it in `entities/me.md`
- If the task turns out to be a project, use `save_note` to update `current-state/projects.md`

## Tips

- Speed matters. Do not spend 10 minutes diagnosing — the goal is motion within 3-5 minutes.
- The agent-as-body-double is powerful. Offering to "stay here while you work" is often enough.
- Never say "just do it." If they could, they would have.
- If the user is stuck on the same task across multiple sessions, gently suggest the decompose-project recipe.

## See Also

- `research/adhd/task-initiation.md` — The science behind activation energy
- `research/adhd/dopamine-systems.md` — Why boring tasks feel impossible
- `research/same-integration/body-double-agent.md` — The agent as accountability partner
- `research/adhd/executive-function.md` — The broader executive function picture
