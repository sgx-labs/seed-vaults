# Personal Productivity OS Seed

## Metadata
- **Name**: personal-productivity-os
- **Type**: Framework
- **Audience**: Knowledge workers, managers, freelancers — anyone who wants an AI-powered personal operating system for work
- **Note count target**: 30-50 (framework seeds are smaller — user fills them)
- **Agent build time**: 1-2 hours

## Purpose
A personal operating system powered by AI agents. Track projects, decisions, people, weekly reviews, and goals — all searchable by your AI assistant. Based on the ops vault pattern: three orientation files, entity-per-concept, trimmed-file depth, self-configuring CLAUDE.md.

## Hub Categories

1. hub/projects.md — Active projects, status, priorities
2. hub/people.md — Key contacts, relationships, communication notes
3. hub/decisions.md — What was decided and why (surfaces in search)
4. hub/weekly-review.md — Weekly review template and process
5. hub/goals.md — Quarterly/annual goals, progress tracking

## Key Questions (templates, not pre-filled answers)

1. What are my active projects and their current status?
2. What decisions have I made recently and why?
3. What do I need to follow up on this week?
4. Who are my key contacts for project X?
5. What are my goals this quarter and how am I tracking?
6. What happened in my last 1:1 with [person]?
7. What are the blockers across my projects?
8. What did I accomplish last week?
9. What's the timeline for [project]?
10. What patterns am I seeing across my work?

## Entities (templates — user fills these)

1. entities/_template-project.md — Template for tracking a project
2. entities/_template-person.md — Template for a key contact
3. entities/_template-tool.md — Template for a tool/service you use

## Pre-Populated Structure

```
personal-productivity-os/
├── CLAUDE.md                    # Self-configuring (fixed + discovery section)
├── bootstrap.md                 # Orientation: how this vault works
├── config.toml.example          # SAME config
├── hub/
│   ├── projects.md              # "What am I working on?"
│   ├── people.md                # "Who do I work with?"
│   ├── decisions.md             # "What have I decided?"
│   ├── weekly-review.md         # "How did this week go?"
│   └── goals.md                 # "What am I trying to achieve?"
├── current-state/
│   ├── active-tasks.md          # Live task list (updated by agent)
│   ├── status.md                # Current focus, blockers, priorities
│   └── timeline.md              # Key dates, milestones, deadlines
├── entities/
│   ├── _template-project.md     # Copy this for each project
│   ├── _template-person.md      # Copy this for each key contact
│   └── _template-tool.md        # Copy this for each tool/service
├── decisions/                   # One file per decision (agent writes these)
├── research/                    # Agent research outputs go here
│   └── _template-research.md    # Template for research notes
├── templates/
│   ├── weekly-review.md         # Weekly review template
│   ├── 1on1-notes.md            # 1:1 meeting notes template
│   ├── project-kickoff.md       # New project kickoff template
│   └── post-mortem.md           # What went wrong and what we learned
├── _Raw/                        # Full-length docs, exports, dumps (not indexed)
├── sessions/                    # SAME session handoffs
└── dashboard.html               # Visual overview (optional)
```

## CLAUDE.md Governance

### Fixed Section (universal)
- This is YOUR personal vault — fill it with your own projects, people, and decisions
- Entity files should be 20-40 lines, updated as things change
- Decisions are first-class — one file per locked decision
- Weekly reviews compound over time — do them consistently
- _Raw/ is for full-length documents you want to keep but not index
- Never put passwords, tokens, or sensitive credentials in any file

### Discovery Section (Claude interviews user on first session)
- What are your current projects? (creates entity files)
- Who are the key people you work with? (creates entity files)
- What tools do you use daily? (creates entity files)
- What does your review cadence look like? (configures templates)
- Any recurring meetings to track? (creates templates)

## Config Notes

```toml
[vault]
  path = "."
  skip_dirs = ["_Raw", "_PRIVATE", "templates"]
  handoff_dir = "sessions"
  decision_log = "decisions/log.md"

[memory]
  max_token_budget = 1200
  max_results = 4
  composite_threshold = 0.65
```

## Why This Seed

- **Largest addressable market** — every knowledge worker, not just developers
- **Framework seed = high retention** — user invests their own content
- **Notion template market is proven** — people pay $30-79 for less than this
- **Demonstrates SAME's write-side value** — decisions, handoffs, entity tracking
- **"Personal OS" is a compelling pitch** — positions SAME beyond developer tooling
