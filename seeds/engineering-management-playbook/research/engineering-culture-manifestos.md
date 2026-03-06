---
title: "Engineering Culture Manifestos That Work"
tags: [culture, values, manifesto, principles, norms, engineering-culture, team-identity]
content_type: research
domain: engineering-management
---

# Engineering Culture Manifestos That Work

## Why Most Culture Docs Fail

"We value innovation, collaboration, and excellence." Congratulations, you've described every company ever. Culture documents fail when they're aspirational platitudes instead of operational principles. A useful culture document tells you what to do when two good values conflict.

## What Makes a Culture Doc Work

### It's Specific Enough to Guide Decisions

**Bad**: "We value quality."
**Good**: "We ship behind feature flags rather than waiting for perfection. If a feature is safe but imperfect, we ship it and iterate. If it's unsafe, we block it regardless of deadline pressure."

### It Acknowledges Tradeoffs

**Bad**: "We value both speed and quality."
**Good**: "When speed and quality conflict, we choose quality for data-touching code and speed for UI experiments. Here's how we decide which category something falls into."

### It Has Concrete Examples

For each principle, include a "this means we DO" and "this means we DON'T":

```
Principle: We optimize for the team, not the individual

This means we DO:
- Write clear PR descriptions even when it's "obvious" to you
- Refactor shared code when we touch it, even if it's not our task
- Participate in on-call rotation regardless of seniority

This means we DON'T:
- Merge our own PRs without review
- Optimize our personal workflow at the expense of team processes
- Hoard knowledge that others need to do their jobs
```

## Building Your Culture Doc

### Step 1: Observe What Already Exists

Culture isn't created from scratch -- it's articulated from what's already true. Watch your team for 2-4 weeks and note:
- How do people handle disagreements?
- What gets celebrated? What gets criticized?
- When someone new joins, what do they find surprising?
- What behaviors do your best engineers consistently demonstrate?

### Step 2: Draft Principles (Not Rules)

Write 5-7 principles. Not 15. Nobody remembers 15 principles.

Each principle should be debatable. "We write tests" is a practice, not a principle. "We invest in automated verification because we trust machines more than checklists" is a principle that generates practices.

### Step 3: Stress-Test Against Real Decisions

For each principle, ask: "Would this have changed the outcome of a recent disagreement?" If no, the principle isn't specific enough.

### Step 4: Get Team Input, Not Team Consensus

Share the draft. Gather input. Incorporate the strong points. But don't let it become a committee document -- committees produce platitudes. One person should own the final voice.

## When NOT to Use This Framework

- **Very early startups (<5 people)**: Culture exists in how you behave, not in a doc. Write it down when you're big enough that new hires can't absorb it through osmosis.
- **As a weapon**: "Our culture doc says X, so you're wrong" is not how this works. Culture docs guide; they don't litigate.
- **Without follow-through**: A culture doc nobody references is worse than no culture doc. If you write it, live it.

## In Reality

The most powerful culture signal isn't the document -- it's what happens when someone violates it. If your doc says "we give direct feedback" but the manager who calls out a VP's bad idea gets sidelined, the doc is fiction.

Culture is what you tolerate. If you tolerate brilliant jerks, your culture is "brilliance excuses bad behavior" regardless of what your manifesto says.

## See Also

- `hub/psychological-safety.md` -- Culture and safety are inseparable
- `hub/hiring-rubric.md` -- Hiring for culture alignment
- `hub/onboarding-engineers.md` -- How new hires absorb culture
- `research/scaling-5-to-50.md` -- Culture changes through growth stages
- `guides/creating-team-charter.md` -- Team-level culture in practice
