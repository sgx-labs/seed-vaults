---
title: "Proactive Agent Patterns"
tags: [same, integration, proactive, patterns, nudges, automation]
content_type: research
domain: productivity
workstream: same-integration
---

# Proactive Agent Patterns

## Reactive vs. Proactive

Most AI interactions are reactive: you ask a question, the agent answers. This is useful, but it leaves the most valuable behaviors on the table. A proactive agent does not wait to be asked — it surfaces information, suggests actions, and notices patterns based on what it knows about you.

SAME enables proactive behavior through its hook system and persistent memory. The agent has context before you speak. It can act on that context without being prompted.

## Pattern 1: Session Start Orientation

**When**: Every session start, via `get_session_context`

The agent loads pinned notes, the last handoff, and recent decisions. Instead of waiting for you to ask "where did I leave off," it opens with orientation:

"Welcome back. Last session you were working on the proposal draft and got through sections 1 and 2. Section 3 is next. You also noted that you need feedback from your manager before finalizing. Your pinned reminder says the deadline is Friday."

This saves 5-10 minutes of re-orientation per session. Over 200 sessions per year, that is 15-30 hours recovered.

## Pattern 2: Stale Project Detection

**When**: Session start, by comparing `current-state/projects.md` timestamps

The agent notices when a project has not been updated in a configurable period:

"Project Alpha has not been updated in 14 days. Last status was 'waiting for client feedback.' Would you like to check in on this, or is it intentionally on hold?"

This catches projects that have silently stalled — the ones that slip through the cracks because no one is tracking them. The agent tracks them.

## Pattern 3: Decision Expiry

**When**: Session start, by scanning decision metadata

Some decisions include conditions for revisiting: "We will revisit pricing after launch." "Try this approach for two weeks and evaluate." The agent can track these:

"You logged a decision on January 15th to use weekly sprints for Project Alpha, with a note to re-evaluate after 30 days. It has been 32 days. Would you like to review this decision?"

This turns one-time decisions into living commitments with built-in review dates. Without this, conditional decisions are forgotten and never revisited.

## Pattern 4: Energy-Aware Suggestions

**When**: Session start, using time of day + `entities/me.md` energy patterns

If the user has completed an energy audit (see `research/energy/energy-audit.md`) and stored their patterns in `entities/me.md`, the agent can match suggestions to energy levels:

"It is Tuesday morning — your peak energy window. You have two deep work tasks pending: the architecture document and the budget analysis. Would you like to tackle one of those now?"

Or conversely:

"It is 3pm and you mentioned that your energy typically dips in the afternoon. The remaining tasks are a code review and an email draft — both good low-energy tasks. Want to start there?"

The agent is not guessing. It is using data you provided about your own patterns.

## Pattern 5: Weekly Review Nudge

**When**: Friday afternoon or the user's configured review day

The agent notices it is the end of the week:

"It is Friday. Would you like to run your 15-minute weekly review? I can pull up this week's accomplishments, pending items, and next week's priorities."

If the user says yes, the agent loads the weekly review template from `templates/weekly-review.md` and populates it with data from handoffs, decisions, and project updates from the past week. The review practically writes itself.

## Pattern 6: Goal Drift Detection

**When**: Monthly or quarterly, by comparing `current-state/goals.md` to activity patterns

The agent compares stated goals to actual activity:

"Your Q1 goal was to launch the new product by March. Based on session activity, most of your time this month has been spent on client support and internal meetings. Only 2 of 12 sessions this month mentioned the product launch. Would you like to revisit your priorities?"

This is not judgment — it is data. The vault knows what you said you wanted to do and what you actually did. The gap between intention and action becomes visible.

## Pattern 7: Connection Surfacing

**When**: During conversation, via `find_similar_notes`

When you create or update a note, the agent searches for related notes you might not have connected:

"This note about user onboarding friction is similar to a note you wrote last month about your own experience with a new tool's setup flow. The pattern you identified — 'too many choices before any value is delivered' — might apply here."

Connections that would take serendipity to discover become systematic.

## Pattern 8: Commitment Tracking

**When**: Session start, by scanning recent handoffs for stated intentions

The agent remembers what you said you would do:

"Last session you said you would send the proposal by Wednesday. It is now Thursday. Did you send it, or do you need to adjust the timeline?"

This is the accountability function of the body double pattern (see `research/same-integration/body-double-agent.md`). The agent does not nag. It notes discrepancies and asks. The decision to act is always yours.

## Implementation Notes

These patterns are not features to be turned on. They are behaviors that emerge from the combination of persistent memory, hooks, and an agent that has been instructed to be proactive. The vault's `CLAUDE.md` governs which patterns the agent employs.

To enable these patterns:
1. Complete the bootstrap discovery in `bootstrap.md` so entity files are populated
2. Use session handoffs consistently so the agent has continuity
3. Log decisions so the agent can reference them
4. Complete an energy audit so time-based suggestions are personalized
5. Set up a weekly review cadence so the agent can nudge it

The patterns compound. Pattern 1 alone saves a few minutes. All eight together create an agent that genuinely knows your work and proactively supports it.

## The Line Between Helpful and Annoying

Proactive suggestions become annoying when they are:
- Too frequent (every session opening with 5 nudges)
- Inaccurate (suggesting deep work when you told the agent you are in meetings all day)
- Repetitive (same suggestion every session without acknowledgment)

The agent should prioritize: surface one or two proactive observations per session, pick the most relevant ones, and retire suggestions that have been acknowledged or dismissed.

## See Also

- `research/same-integration/body-double-agent.md` — The accountability model
- `research/same-integration/context-surfacing-patterns.md` — How context is surfaced
- `research/same-integration/same-as-executive-function.md` — Proactive patterns as executive function
- `research/energy/energy-audit.md` — Building the energy profile
- `research/reviews/weekly-review-15min.md` — The weekly review framework
