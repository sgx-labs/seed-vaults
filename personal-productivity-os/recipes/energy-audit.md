---
title: "Energy Audit — Map Your Energy Patterns"
tags: [recipe, energy, audit, patterns, focus, deep-work]
content_type: recipe
domain: productivity
---

# Energy Audit — Map Your Patterns

## Purpose

Map energy patterns across a typical week so you can match work to your natural rhythms. Most people schedule by availability. This recipe helps you schedule by capacity.

## When to Use

- First time setting up the productivity OS (part of discovery)
- Quarterly re-calibration of energy patterns
- After a major life change (new job, new schedule, health shift)
- When the user says "I feel like I am always working at the wrong time"

## The Script

### Step 1: Set Expectations

> "We are going to walk through your typical week and map where your energy is high, medium, and low. This takes about 10-15 minutes and will help me suggest the right work at the right time."

### Step 2: Walk Through Each Day

For each weekday, ask about three time blocks:

> "Let's start with Monday. In the morning, how is your energy usually — sharp and focused, moderate, or low? What about the afternoon? And the evening?"

Build a simple map as you go:

| Day | Morning | Afternoon | Evening |
|-----|---------|-----------|---------|
| Mon | | | |
| Tue | | | |
| Wed | | | |
| Thu | | | |
| Fri | | | |

Use three levels: High, Medium, Low. Do not over-complicate it.

### Step 3: Map Task Types to Energy

Ask:

> "What kind of work do you do that requires your sharpest thinking? What about work that is moderate effort? And what can you do on autopilot?"

Create a mapping:
- **High energy work**: deep thinking, writing, creative work, hard decisions
- **Medium energy work**: meetings, collaboration, planning, communication
- **Low energy work**: email, admin, organizing, routine tasks

### Step 4: Identify Energy Givers and Drainers

Ask:

> "What activities give you energy — even if they are technically work? And what drains you, even if it is 'supposed' to be easy?"

Listen for:
- Specific tasks or meetings that are unusually draining
- Activities that feel energizing despite being effortful
- Environmental factors (noise, people, location)
- Time-of-day effects ("I love mornings but afternoons kill me")

### Step 5: Create the Ideal Schedule

Based on the mapping, suggest an ideal schedule:

> "Based on what you told me, here is how your week could look: [morning blocks for deep work on high-energy days], [meetings clustered in medium-energy slots], [admin during low-energy periods]. What do you think?"

Be realistic — they cannot control everything. Focus on the changes within their control.

### Step 6: Run Research Search

Run `search_notes(query="energy management deep work scheduling", top_k=5)`. Surface one relevant note:

> "There is a research note on [topic] that connects to what you described. [One-sentence summary]. Want to see it?"

### Step 7: Update the Profile

Update `entities/me.md` with the energy profile using `save_note`:

Add a section like:
```
## Energy Patterns
- Peak hours: [times]
- Low-energy periods: [times]
- Energy givers: [list]
- Energy drainers: [list]
- Best deep work window: [time]
- Ideal meeting window: [time]
```

Confirm:

> "I have updated your profile with your energy patterns. I will use this to suggest the right work at the right time."

## What Gets Updated

- `entities/me.md` — energy profile added or refreshed
- Nothing else by default, unless the user wants to restructure their schedule

## Tips

- Do not make this feel like a medical intake form. Keep it conversational.
- Weekend patterns are optional. Only ask if the user works on weekends.
- Energy patterns change. Flag this for quarterly re-audit.
- If the user mentions caffeine, sleep, or exercise as major factors, note those too.
- Some people have consistent patterns. Others fluctuate wildly. Both are normal.

## See Also

- `research/energy/energy-audit.md` — The full energy audit framework
- `research/energy/deep-work-flow.md` — Protecting peak hours
- `research/energy/burnout-prevention.md` — Sustainable work patterns
- `research/energy/context-switching-cost.md` — Why fragmented days drain energy
