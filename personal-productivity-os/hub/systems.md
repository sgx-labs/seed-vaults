---
title: "Systems — Capture, Organize, Review"
tags: [systems, capture, organize, review, gtd, productivity]
content_type: hub
domain: productivity
---

# Systems — Capture, Organize, Review

## Overview

A productivity system is a workflow for managing tasks, projects, and information so nothing falls through the cracks. Whether you follow GTD (Getting Things Done), time blocking, or a minimum viable approach, every effective system rests on three pillars: capture inputs into a trusted inbox, organize them into next actions and project lists, and review regularly to keep the system current. This hub covers task management frameworks, time management strategies, and the habits that make any workflow sustainable.

## The Three Pillars

### 1. Capture
Get every idea, task, and commitment out of your head and into a trusted system. Your brain is for processing, not storage.

See: `research/systems/quick-capture.md`, `research/systems/two-minute-rule.md`

### 2. Organize
Turn raw captures into next actions, projects, and reference material. Everything needs a clear "what is the next step?"

See: `research/systems/gtd-essentials.md`, `research/systems/organize-not-sort.md`

### 3. Review
The system only works if you trust it. You only trust it if you review it regularly. Weekly review is the minimum viable cadence.

See: `hub/reviews.md`, `research/reviews/weekly-review-15min.md`

## Framework Comparison

| System | Core Idea | Best For | Risk |
|--------|-----------|----------|------|
| GTD | Capture everything, next actions | Comprehensive tracking | Complexity overhead |
| Time Blocking | Assign tasks to calendar blocks | Deep work, focus | Rigidity when plans change |
| Flexible Scheduling | Priority list, do what fits | Variable workdays | Easy to procrastinate |
| Minimum Viable | Capture + weekly review only | Getting started | May need more structure later |

See: `research/systems/time-blocking-vs-flexible.md`, `research/systems/minimum-viable-system.md`

## Your System (Customize This)

The agent should learn your preferences through discovery and help you maintain whatever system you choose. There is no correct answer — only what you will actually do consistently.

## Research Notes

- `research/systems/gtd-essentials.md` — GTD distilled to what matters
- `research/systems/quick-capture.md` — The 2-minute rule and capture habits
- `research/systems/two-minute-rule.md` — If it takes under 2 minutes, do it now
- `research/systems/time-blocking-vs-flexible.md` — Structured vs. flexible scheduling
- `research/systems/organize-not-sort.md` — Organizing means deciding next actions
- `research/systems/minimum-viable-system.md` — The simplest system that works
- `research/systems/information-diet.md` — Reducing noise and inputs

---

## Dashboards & Tracking Systems

### 4. Track (Living Dashboards)

The agent maintains a set of "living dashboards" — markdown files that stay current because the agent updates them during conversation. You never open these files directly. You never manually edit a status. The agent handles all of it as a natural consequence of working with you.

This is the key insight: **the agent IS the dashboard update mechanism.** Zero maintenance cost.

See: `research/systems/living-dashboards.md`

### Dashboard Templates

Five ready-to-use dashboards in `templates/dashboards/`:

| Dashboard | Purpose | Update Cadence |
|-----------|---------|----------------|
| `weekly-dashboard.md` | Week-at-a-glance command center | Monday setup, Friday review |
| `project-tracker.md` | Master view of all projects | Any project status change |
| `energy-map.md` | Energy patterns and scheduling insights | Agent asks periodically |
| `goals-progress.md` | Quarterly goals with key results | Weekly review, milestone updates |
| `decision-dashboard.md` | Decisions made and pending | Any significant decision |

### How the Agent Uses Dashboards

1. **Session start** — reads dashboards for orientation (knows your current state)
2. **During conversation** — updates dashboards when things change (project completes, decision made, energy reported)
3. **Weekly review** — synthesizes all dashboards into a review, asks targeted questions
4. **Monthly review** — identifies patterns across weeks, suggests adjustments

See: `research/systems/review-automation.md`

### Dashboard Research Notes

- `research/systems/living-dashboards.md` — How agent-maintained dashboards work and why they beat traditional tracking
- `research/systems/progress-visualization.md` — Unicode progress bars, text markers, tables as dashboards
- `research/systems/review-automation.md` — How the agent automates weekly, monthly, and quarterly reviews
