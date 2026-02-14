---
title: "Progress Visualization in Plain Markdown"
tags: [dashboards, visualization, markdown, progress, tracking]
content_type: research
domain: productivity
---

# Progress Visualization in Plain Markdown

## The Challenge

Markdown has no charts, no color coding, no interactive widgets. But it
does have tables, Unicode characters, and clean formatting. With the right
techniques, you can build dashboards that are genuinely useful and
pleasant to read — without leaving plain text.

## Technique 1: Unicode Progress Bars

Use block characters to create visual progress indicators:

```
Full block:  # (hash characters in brackets work universally)
Empty block: . (dots for remaining)
```

**Standard format:**

| Progress          | Visual                        |
|-------------------|-------------------------------|
| 0%                | `[....................]`       |
| 25%               | `[#####...............]`       |
| 50%               | `[##########..........]`       |
| 75%               | `[###############.....]`       |
| 100%              | `[####################]`       |

**Compact format for tables:**

| Task              | Done  | Bar                     |
|-------------------|-------|-------------------------|
| Research phase    | 100%  | `[########]`            |
| Draft writing     | 60%   | `[#####...]`            |
| Review cycle      | 25%   | `[##......]`            |
| Final edits       | 0%    | `[........]`            |

The agent calculates the correct number of filled/empty characters based
on the actual percentage.

## Technique 2: Text Status Markers

Instead of colored circles or emoji, use clear text markers in brackets:

**Project status:**
- `[ACTIVE]` — Currently being worked on
- `[PAUSED]` — Intentionally on hold
- `[BLOCKED]` — Cannot proceed, waiting on something
- `[DONE]` — Completed

**Task status:**
- `[NOT STARTED]` — Has not begun
- `[IN PROGRESS]` — Underway
- `[ON TRACK]` — Progressing as planned
- `[AT RISK]` — May miss target
- `[BEHIND]` — Has missed a milestone
- `[COMPLETE]` — Finished

**Priority markers:**
- `[!!!]` — Critical / must do today
- `[!!]` — High priority / this week
- `[!]` — Normal priority
- `[~]` — Low priority / nice to have

## Technique 3: Tables as Dashboards

Markdown tables are the primary layout tool for dashboards. Key principles:

**Alignment matters.** Consistent column widths make tables scannable:

| Project        | Status     | Progress                  | Next Action              |
|----------------|------------|---------------------------|--------------------------|
| Project Alpha  | [ACTIVE]   | `[############........]`  | Send draft to reviewers  |
| Side Project   | [PAUSED]   | `[########............]`  | Revisit in March         |
| Learning: Go   | [ACTIVE]   | `[###############.....]`  | Complete module 4        |

**Use horizontal rules to create sections within a document.** Three dashes
on their own line create visual breathing room between dashboard sections.

## Technique 4: Streak Counters

Track consistency with simple counters:

```
Exercise streak:    12 days (current)
Best streak:        21 days (Jan 5 - Jan 26)
This month:         8 / 14 days (57%)
```

**Weekly streak grid:**

| Week       | M | T | W | T | F | S | S | Total |
|------------|---|---|---|---|---|---|---|-------|
| Feb 10-16  | x | x | . | x | x | . | . | 4/5   |
| Feb 3-9    | x | . | x | x | x | x | . | 5/5   |
| Jan 27-Feb2| x | x | x | . | x | . | . | 4/5   |

Use `x` for completed and `.` for missed. Clean and scannable.

## Technique 5: Sparkline-Style Summaries

Represent trends in a single line using simple characters:

```
Energy trend (last 7 days):  _ - ^ ^ - _ _
Revenue trend (last 6 mo):   - - ^ ^ ^ ^
Mood trend (this week):       ^ ^ - ^ -
```

Key: `^` = up/high, `-` = flat/medium, `_` = down/low

This is not a real chart, but it communicates direction at a glance.

## Technique 6: Section Headers as Navigation

In a long dashboard, use clear markdown headers to create a scannable
structure. When the agent reads the file, it can jump to the relevant
section. When a human reads it, the table of contents (in most markdown
renderers) provides instant navigation.

```markdown
## Active Projects          <-- jump here for project status
## Waiting On               <-- jump here for blocked items
## This Week's Priorities   <-- jump here for what to focus on
## Energy Check             <-- jump here for energy data
```

## Putting It Together

The best markdown dashboards combine these techniques:

1. **Tables** for structured data (projects, goals, decisions)
2. **Progress bars** for visual completion tracking
3. **Text markers** for status at a glance
4. **Horizontal rules** for visual separation between sections
5. **Streak counters** for consistency tracking
6. **Clear headers** for navigation

The result is a plain text file that communicates as effectively as a
graphical dashboard — and that an agent can read and update natively.

See: `templates/dashboards/`, `research/systems/living-dashboards.md`
