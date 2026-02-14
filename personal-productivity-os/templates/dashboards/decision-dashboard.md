---
title: "Decision Dashboard"
tags: [template, dashboard, decisions, tracking]
content_type: template
domain: productivity
---

# Decision Dashboard

> Every significant decision in one place. The agent logs decisions here
> during conversation and references this when a topic comes up again.
> Prevents the costly loop of re-debating settled questions.

---

## Decisions Pending

_These need resolution. The agent will surface them during reviews._

| Decision Needed                          | Context                            | Since      | Urgency    |
|------------------------------------------|------------------------------------|------------|------------|
| Choose hosting provider for Project Alpha | Vendor A is cheaper, B has support | 2026-02-05 | This week  |
| Decide on Q2 learning focus              | Go deeper on Go vs. start Rust     | 2026-02-10 | This month |
| Accept or decline advisory board invite  | Time commitment is 4 hrs/month     | 2026-02-12 | By Feb 21  |

### Decision-Making Shortcuts

If a pending decision has been open for more than 2 weeks, the agent will prompt:
> "This has been pending since [date]. What information would help you decide?
> Or is this a two-way door you can just walk through and adjust later?"

---

## Recent Decisions

| # | Decision                                          | Date       | Type       | Status     |
|---|---------------------------------------------------|------------|------------|------------|
| 12| Declined the conference speaking invitation       | 2026-02-11 | Commitment | [ACTIVE]   |
| 11| Set Friday afternoons as no-meeting time           | 2026-02-08 | Process    | [ACTIVE]   |
| 10| Chose SQLite over Postgres for side project       | 2026-02-05 | Technical  | [ACTIVE]   |
| 9 | Committed to 4x/week exercise target              | 2026-02-01 | Personal   | [ACTIVE]   |
| 8 | Paused home renovation until March                | 2026-01-28 | Project    | [ACTIVE]   |
| 7 | Switched to time-blocking for deep work           | 2026-01-22 | Process    | [ACTIVE]   |
| 6 | Dropped the freelance client project              | 2026-01-18 | Commitment | [ACTIVE]   |
| 5 | Set quarterly goals using OKR framework           | 2026-01-15 | Process    | [ACTIVE]   |

---

## Decision Detail — Recent

### #12 — Declined conference speaking invitation

- **Date:** 2026-02-11
- **Context:** Invited to speak at a regional tech conference in April. Topic would be interesting but prep time is 20+ hours.
- **Rationale:** Q1 is already full with the product launch. Speaking would compete directly with the top priority. Revisit for Q3 conferences.
- **Impact:** Freed up mental bandwidth. No regrets so far.
- **Revisit:** After product launch ships (March). Open to Q3 opportunities.

### #11 — Set Friday afternoons as no-meeting time

- **Date:** 2026-02-08
- **Context:** Friday afternoons were consistently low energy (see energy map). Meetings during this window were unproductive.
- **Rationale:** Better to use this time for low-stakes admin, weekly review, and cleanup. Protects the rest of the week from spillover.
- **Impact:** Two weeks in — noticeably less end-of-week stress.
- **Revisit:** End of February. If it is working, make it permanent.

### #10 — Chose SQLite over Postgres for side project

- **Date:** 2026-02-05
- **Context:** Side Project Beta needed a database. Postgres is more powerful but SQLite means zero infrastructure and the data set is small.
- **Rationale:** This is a personal project. Simplicity wins. Can migrate later if needed (two-way door).
- **Impact:** Setup time went from estimated 4 hours to 20 minutes.
- **Revisit:** Only if data volume exceeds 1GB or concurrent access is needed.

---

## Decisions to Revisit

_Decisions flagged for future review._

| Decision                                  | Original Date | Revisit By | Reason                         |
|-------------------------------------------|---------------|------------|--------------------------------|
| Time-blocking for deep work (#7)          | 2026-01-22    | 2026-02-28 | Check if it stuck after 5 weeks|
| Paused home renovation (#8)               | 2026-01-28    | 2026-03-01 | Re-evaluate timeline           |
| No Friday afternoon meetings (#11)        | 2026-02-08    | 2026-02-28 | Confirm it is helping          |

---

## How This Works

When you make a decision during a conversation, the agent asks:

> "Should I log this decision? It sounds significant."

If yes, it gets added here with date, context, rationale, and a revisit date
if appropriate. When the same topic comes up later, the agent references the
original decision:

> "We decided this on [date]. The reasoning was [X]. Want to revisit,
> or does the original decision still hold?"

This prevents the energy drain of re-litigating settled questions and builds
a record you can learn from over time.

---

_Last updated by agent: [TIMESTAMP]_
