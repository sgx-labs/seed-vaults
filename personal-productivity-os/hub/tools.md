---
title: "Tools — Your Productivity Stack"
tags: [hub, tools, claude-code, same, mcp, workflows, skills]
content_type: hub
domain: productivity
---

# Tools — Your Productivity Stack

## Overview

Your productivity tools form a digital workspace where note-taking, task management, calendar blocking, and automation come together. This hub covers tool selection for your workflow, integrations between Claude Code, SAME, and your existing apps, and recommended productivity tools for capture, focus, and accountability. The best tool stack reduces friction, automates repetitive steps, and stays out of your way so you can focus on the work itself.

## The Core Stack

### Claude Code — Your Interface

Claude Code is your primary way of interacting with your vault. It is a terminal-based AI agent that can read files, search, edit, and execute commands. Through SAME integration, it has persistent memory across sessions.

What Claude Code does:
- Reads and writes vault notes
- Runs SAME MCP tools (search, decisions, handoffs)
- Executes slash commands and skills
- Processes context from hooks at session start and on every prompt

### SAME — Your Memory Layer

SAME is the engine that gives Claude Code persistent memory. Without it, every conversation starts from zero. With it, the agent knows your projects, decisions, patterns, and priorities.

What SAME does:
- Indexes your vault for semantic search
- Manages session handoffs (continuity between conversations)
- Logs decisions with full rationale
- Surfaces relevant context through hooks
- Searches across multiple vaults

See: `hub/same-mastery.md`

### How They Connect

```
You <-> Claude Code <-> SAME MCP Server <-> Your Vault (markdown + SQLite)
         |                    |
         |                    +-- Hooks fire on session start and prompts
         +-- Slash commands and skills extend capabilities
```

The connection is through MCP (Model Context Protocol). SAME runs as an MCP server that Claude Code calls. Hooks are configured in Claude Code's settings to fire SAME tools automatically — you do not have to remember to invoke them.

## Recommended Productivity Tools

| Tool | Category | How It Connects to Your Vault |
|------|----------|------------------------------|
| **Visual timers** (Time Timer, Focus To-Do) | Focus | Use with time-boxing strategies from `research/adhd/time-blindness.md` |
| **Pomodoro apps** (Forest, Flow) | Focus | Pairs with deep work blocks from `research/energy/deep-work-flow.md` |
| **Body doubling platforms** (Focusmate, Flown) | Accountability | Complements the agent as body double — see `research/same-integration/body-double-agent.md` |
| **Quick capture** (Apple Notes, voice memo) | Capture | Dump ideas fast, process into vault during review — see `research/systems/quick-capture.md` |
| **Calendar blocking** | Planning | Time-block your week, reference in weekly review — see `research/systems/time-blocking-vs-flexible.md` |
| **Distraction blockers** (Cold Turkey, one sec) | Focus | Remove escape routes during deep work sessions |
| **Physical notebook** | Thinking | Some thinking is better on paper; digitize decisions into the vault |

## Claude Code Skills for Productivity

Skills are slash commands that extend Claude Code with specialized capabilities. Useful ones for this vault:

| Skill | What It Does | When to Use It |
|-------|-------------|----------------|
| `/commit` | Creates a git commit with conventional message | After editing vault files |
| `/review-pr` | Reviews a pull request | Code project reviews |
| Custom skills | User-defined workflows | Build your own for repeated tasks |

## Recommended Workflows

### Morning Orientation (2 minutes)

1. Open Claude Code in your vault directory
2. Session hook fires `get_session_context` automatically
3. Agent presents: last handoff, pinned notes, recent decisions
4. You now know what happened, what is pending, and what matters today

### Deep Work Session

1. Tell the agent what you are focusing on
2. Agent searches vault for related context, past work, and relevant decisions
3. Work with the agent as a body double — it holds context while you execute
4. Agent captures progress in notes as you go

### Decision Point

1. Present the decision to the agent
2. Agent searches for prior related decisions in the vault
3. Discuss options, weigh tradeoffs
4. Agent logs the decision with `save_decision` — rationale, alternatives, status

### Weekly Review (15 minutes)

1. Open the weekly review template: `templates/weekly-review.md`
2. Agent pulls project status from `current-state/projects.md`
3. Review accomplishments, blockers, and upcoming priorities
4. Agent updates current state files and creates handoff
5. Captures wins and lessons for pattern recognition over time

### End of Day Handoff (1 minute)

1. Tell the agent what you accomplished and what is pending
2. Agent creates handoff via `create_handoff`
3. Tomorrow's session starts with full context — no cold start

### Processing Quick Captures

1. Review items captured throughout the day (phone notes, voice memos, sticky notes)
2. For each: is it a task, a decision, a reference, or trash?
3. Tasks go to `current-state/projects.md`
4. Decisions go to `decisions/log.md` via `save_decision`
5. Reference goes to a research note via `save_note`
6. Trash gets deleted — see `research/systems/organize-not-sort.md`

## Building Your Own Workflows

The tools above are starting points. The best workflow is the one you actually do. Principles:

1. **Reduce friction** — Every extra step is a reason to skip the workflow
2. **Automate what you can** — Hooks and templates exist so you do not have to remember steps
3. **Start minimal** — Use the morning orientation and end-of-day handoff first; add complexity only when needed
4. **Let the agent adapt** — Over time, the agent learns your patterns and can suggest workflow adjustments

## Related Hub Notes

- `hub/same-mastery.md` — Deep dive into SAME features, MCP tools, and vault structure
- `hub/systems.md` — The system your tools serve — capture, organize, review
- `hub/energy.md` — Tool choices should match your energy management strategy
- `hub/adhd.md` — Tool recommendations calibrated for ADHD brains
- `hub/reviews.md` — The weekly review is the workflow that holds everything together
