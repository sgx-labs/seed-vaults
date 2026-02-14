---
title: "Decompose Project — Break Down Complex Work"
tags: [recipe, project, decomposition, milestones, planning]
content_type: recipe
domain: productivity
---

# Decompose Project — From Vague to Actionable

## Purpose

Turn a vague project into a structured plan with clear milestones, next actions, and decisions. By the end of this recipe, the project has a definition of done, 3-5 milestones, and a concrete first step.

## When to Use

- "I need to do [big vague thing] but I do not know where to start"
- A new project has been added without a clear plan
- An existing project has stalled because the scope is unclear
- After a weekly review flags a project with no defined next action

## Light Version (5 minutes)

For low-energy days or when you just need to get moving:

1. **What's the outcome?** One sentence: what does "done" look like?
2. **What's the first milestone?** The first meaningful checkpoint.
3. **What's the very next action?** Something you can do in under 30 minutes.
4. **Save it**: Update `current-state/projects.md` with this project.

That's it. The full decomposition below is optional — come back to it when you have more energy.

---

## Full Version (15-20 minutes)

### Step 1: Define the Outcome

Ask:

> "When this project is completely done, what does that look like? Describe the end state in one or two sentences."

If the answer is vague ("it is done when the website is better"), push for specifics:

> "What would someone see, use, or experience that tells you this is finished?"

Write the definition of done as a single clear statement.

### Step 2: Set the Scope Boundary

Ask:

> "What is explicitly NOT part of this project? What are you choosing to leave out?"

This prevents scope creep. Document at least 2-3 things that are out of scope.

### Step 3: Identify Milestones

Ask:

> "If you had to break this into 3-5 major chunks that you would finish one at a time, what would they be?"

Help them think in terms of deliverables, not activities. Each milestone should be something you can point to and say "this part is done."

For each milestone, capture:
- What it delivers
- Rough effort estimate (hours or days, not precise)
- Dependencies on other milestones or external factors

### Step 4: Define Next Actions per Milestone

For each milestone, ask:

> "What is the very first thing you would need to do to start this milestone?"

Each next action should be a single concrete step — "email the vendor for a quote," not "figure out the vendor situation." If they cannot name a next action, the milestone is too vague and needs further decomposition.

### Step 5: Surface Decisions Needed

Ask:

> "Are there any decisions you need to make before you can move forward? Anything where you are choosing between options?"

For each decision identified:
- Note what the decision is
- List known options
- If the user can decide now, walk through it and log with `save_decision(title="...", body="...", status="accepted")`
- If they need more information, note it as a blocker

### Step 6: Check for Dependencies

Ask:

> "Is any of this waiting on someone else or something outside your control?"

Document dependencies. Flag any that could block the first milestone.

### Step 7: Update the Vault

1. Update `entities/projects.md` with the project entry: name, definition of done, milestones
2. Update `current-state/projects.md` with the project status, current milestone, and next action
3. Use `save_note` for both files

Confirm with the user:

> "Here is the plan: [milestone count] milestones, first action is [action]. The project is now tracked. Anything to adjust?"

## What Gets Updated

- `entities/projects.md` — new or updated project entry with milestones
- `current-state/projects.md` — live status with next action
- `decisions/` — any decisions made during decomposition via `save_decision`

## Tips

- Most projects need 3-5 milestones. If you have more than 7, the project might actually be multiple projects.
- The first milestone should be achievable within a week. If not, break it down further.
- If the user gets overwhelmed during decomposition, pause and use the unstick-me recipe.
- Time estimates are rough guides, not commitments. Do not over-engineer the plan.

## See Also

- `research/systems/organize-not-sort.md` — Next actions, not task lists
- `research/goals/okr-framework.md` — Connecting projects to goals
- `hub/projects.md` — Project lifecycle and health checks
- `research/adhd/executive-function.md` — Why decomposition is hard for some brains
