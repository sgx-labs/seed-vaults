---
title: "ADHD-Friendly Systems: Less Is More"
tags: [adhd, systems, productivity, capture, flexibility, minimum-viable, gtd]
content_type: research
domain: productivity
workstream: adhd
---

# ADHD-Friendly Systems: Less Is More

## Why Most Productivity Systems Fail for ADHD

The productivity industry is built for neurotypical brains. Most systems assume you can:
- Maintain consistent daily habits
- Follow multi-step processes reliably
- Review and update your system regularly
- Resist the urge to redesign the system itself

ADHD brains do none of these consistently. The result is a graveyard of abandoned systems: GTD setups that lasted two weeks, bullet journals with three pages filled, Notion databases that became their own project, kanban boards nobody updates.

The system is not the problem. The fit is.

## What Makes a System ADHD-Friendly

### 1. Simple Capture
The single most important feature: capturing a thought must take less than 5 seconds. If capture requires opening an app, navigating to a page, choosing a category, and typing — you will not do it. The thought evaporates.

What works:
- A single inbox (one text file, one app, one physical tray)
- Voice capture (dictate, process later)
- Quick-entry shortcuts (keyboard shortcut to task app)
- The "dump everything here, sort later" approach

### 2. Flexible Structure
Rigid schedules break on day one. Flexible structure means:
- Fixed anchor points (morning planning, end-of-day review) with loose time between
- Categories based on energy state, not time: "high energy tasks," "low energy tasks," "creative tasks"
- Permission to change the plan when your brain changes
- 20-40% buffer time built into every day

### 3. External Accountability
ADHD brains are remarkably responsive to social accountability. The system should include:
- Someone or something that checks on you
- Visible commitments (shared calendars, public goals)
- Regular check-ins (weekly reviews with a partner, daily standups)

### 4. Visible Progress
If you cannot see that you are making progress, your dopamine system has no fuel. The system should make progress obvious:
- Checked-off items that stay visible (do not auto-hide completed tasks)
- Progress bars, streaks, or completion counts
- A "done" list, not just a "to-do" list

### 5. Forgiveness for Inconsistency
The system must survive your worst day. If missing one day breaks the chain, the system is too fragile. Design for:
- Easy restart after a gap (no need to "catch up")
- No guilt for skipped reviews
- Rolling priorities that carry forward automatically

## The Minimum Viable ADHD System

If you are starting from zero or rebuilding after a system collapse, here is the simplest system that works:

### Daily (5 minutes)
1. Open your one capture tool
2. Look at today's list
3. Pick the ONE thing that matters most
4. Do that first

### Weekly (15 minutes)
1. Process your inbox (delete, do, defer, delegate)
2. Check upcoming deadlines
3. Pick 3 priorities for the week
4. Notice what is working and what is not

### Monthly (30 minutes)
1. Review what you accomplished
2. Check alignment with goals
3. Adjust your approach
4. Celebrate progress

That is it. Everything else is optional. Add complexity only when you have a specific problem the minimal system does not solve, and only one thing at a time.

## Common ADHD System Traps

### The Setup Trap
Spending days designing the perfect system instead of using a simple one. The setup IS the dopamine hit. The system never gets used.

**Fix:** Give yourself 30 minutes to set up. Use it for a week before changing anything.

### The Novelty Trap
Switching to a new tool or system every month because the current one got boring. This is your dopamine system seeking novelty, not a real problem with the tool.

**Fix:** Commit to a tool for 90 days. Customize it for novelty instead of replacing it.

### The Complexity Trap
Adding tags, contexts, priority levels, color codes, and custom fields until the system requires more executive function than the work itself.

**Fix:** If you cannot explain your system in two sentences, simplify it.

### The Consistency Trap
Quitting a system because you missed a few days. "I already ruined it, so why bother?"

**Fix:** Design for restart, not streaks. Every day is day one.

## How SAME Helps

SAME provides the backbone of an ADHD-friendly system without the overhead:

- **Zero-maintenance capture** — Session handoffs capture context automatically. You do not have to remember to write things down. The `create_handoff` call at session end preserves what was done, what is pending, and what is blocking.
- **Automatic retrieval** — `get_session_context` loads your priorities, pending work, and recent decisions every session. No manual review required. The system comes to you.
- **Flexible, not rigid** — SAME does not impose a workflow. It captures whatever happened and makes it findable. If you worked on something unexpected, that is captured too. No guilt for going off-plan.
- **Survives inconsistency** — Skip a week? The agent still has your context from the last session. No "catching up." No inbox of 500 unprocessed items. Just pick up where you left off.
- **Progressive complexity** — Start with just handoffs. Add pinned notes when you want persistent priorities. Add decision logs when you are making choices you want to remember. The system grows with your needs.
- **Search over structure** — You never have to organize your notes into folders, categories, or tags. Semantic search finds what you need by meaning. Ask a question, get an answer. The filing happens behind the scenes.
- **The agent as accountability partner** — The agent surfaces your priorities every session. It is a gentle, persistent accountability system that never judges you for falling behind.

SAME is designed to be the system that survives the ADHD cycle of enthusiasm, inconsistency, and restart. It works when you are on fire and it works when you are struggling. The bar for participation is showing up to a session.

See: `research/adhd/working-memory.md`, `research/systems/minimum-viable-system.md`
