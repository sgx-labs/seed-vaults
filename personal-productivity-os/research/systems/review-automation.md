---
title: "Review Automation — The Agent Does the Grunt Work"
tags: [reviews, automation, agent, weekly-review, monthly-review, dashboards]
content_type: research
domain: productivity
---

# Review Automation — The Agent Does the Grunt Work

## Why Reviews Fail

The weekly review is the single most important productivity habit. It is
also the one most people skip. The reason is not laziness — it is that
traditional reviews require you to do all the work: open every project,
check every status, remember what happened, synthesize it, then plan.
That is 30-60 minutes of administrative overhead that feels like a chore.

## The Agent-Assisted Review

With an agent maintaining your dashboards, the review flips. The agent
does the gathering, synthesizing, and drafting. You just answer questions
and make decisions.

### Weekly Review (15 minutes instead of 45)

**What the agent does before you start:**

1. Reads the project tracker — identifies what moved, what is stuck
2. Reads the energy map — spots patterns from the past week
3. Reads the goals dashboard — calculates progress since last review
4. Reads the decision dashboard — checks for pending decisions
5. Reads the weekly dashboard — compares planned priorities to actual outcomes

**What the agent presents to you:**

> "Here is your week in review:
>
> **Projects:** Project Alpha advanced from 35% to 40%. Side Project Beta
> had no movement this week. Home Renovation is still paused.
>
> **Goals:** You exercised 4 times this week (target: 4). You finished
> one book (on track for quarterly target). Decision logging is strong.
>
> **Energy:** Your best days were Tuesday and Wednesday. Friday was your
> lowest energy day again. You skipped the post-lunch walk on Monday and
> Thursday — those were your worst afternoons.
>
> **Pending decisions:** The hosting provider choice has been open for
> 9 days. The Q2 learning focus has been open for 4 days.
>
> **Questions for you:**
> 1. What were your wins this week?
> 2. Side Project Beta did not move — is that intentional or a problem?
> 3. Ready to decide on the hosting provider?
> 4. What are your top 3 priorities for next week?"

You answer four questions. The agent updates all the dashboards. The
review is done in 15 minutes.

### Monthly Review (30 minutes instead of 90)

**What the agent does:**

1. Reads the last 4 weekly dashboards
2. Compiles a month-level summary of project progress
3. Reads the goals dashboard — calculates monthly trajectory
4. Reads the energy map — identifies month-long patterns
5. Reads the decision log — counts decisions made, checks revisit dates

**What the agent presents:**

> "February in review:
>
> **Projects:** Project Alpha went from 25% to 45%. Side Project Beta
> went from 70% to 75% — slower than planned. You completed the Q4
> Annual Review.
>
> **Goals:** You are on track for 3 of 4 quarterly goals. The exercise
> goal is the strongest. The reading goal needs acceleration — you need
> 8 more books in 6 weeks.
>
> **Energy patterns:** Your average energy score improved from 2.0 to
> 2.3 this month. The no-meeting Friday policy is working. Your worst
> day continues to be Monday after a weekend with poor sleep.
>
> **Decisions:** You made 7 significant decisions this month. Two are
> flagged for revisit: time-blocking (due Feb 28) and the renovation
> pause (due Mar 1).
>
> **Questions for you:**
> 1. Are your quarterly goals still the right goals?
> 2. Do you want to adjust the reading target or push harder?
> 3. What was your biggest lesson this month?
> 4. What do you want to do differently in March?"

### Quarterly Review (60 minutes instead of half a day)

Same pattern, bigger scope. The agent reads 3 months of weekly and monthly
data, presents the trajectory, and asks strategic questions:

- Which goals did you hit? Which did you miss? Why?
- What projects should continue, pause, or stop?
- What patterns in your energy and decisions should inform next quarter?
- What are the right goals for Q2?

## The Principle: Gather Automatically, Decide Manually

The agent handles everything that is mechanical:
- Reading files and extracting status
- Calculating progress percentages
- Spotting patterns across time
- Drafting summaries and comparisons
- Surfacing items that need attention

You handle everything that requires judgment:
- What were the real wins?
- What should change?
- What are next week's priorities?
- Is this goal still worth pursuing?

This division of labor is what makes reviews sustainable. The agent
removes the drudgery. You provide the insight.

## How SAME Enables This

| SAME Feature       | Review Function                                    |
|--------------------|----------------------------------------------------|
| Session context    | Agent arrives already knowing your dashboard state  |
| Note search        | Agent can pull any historical review or decision    |
| Note saving        | Agent writes updates directly to dashboards         |
| Handoffs           | Review progress carries across sessions             |
| Federated search   | Agent can check across multiple vaults if needed    |

## Getting Started

You do not need to set up a review process. The agent will:

1. Notice when it has been 7 days since the last weekly review
2. Offer to run the review with you
3. Walk through the questions above
4. Update all dashboards based on your answers

The first review takes longer because the agent is learning your
preferences. By the fourth week, the process is smooth and fast.

See: `templates/dashboards/`, `templates/weekly-review.md`, `hub/reviews.md`
