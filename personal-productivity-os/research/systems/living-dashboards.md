---
title: "Living Dashboards — Agent-Maintained Command Centers"
tags: [dashboards, tracking, agent, automation, SAME]
content_type: research
domain: productivity
---

# Living Dashboards — Agent-Maintained Command Centers

## The Problem with Dashboards

Traditional dashboards fail for individuals. Not because the concept is
wrong, but because the maintenance cost is too high. You set up a Notion
board, a spreadsheet tracker, or a project management tool. It looks great
on day one. By week three, it is stale. By month two, you stop opening it.

The failure mode is always the same: **the update burden falls on the
person who is already busy.**

## The Solution: The Agent IS the Update Mechanism

A living dashboard is a markdown file that the agent reads at the start
of every session and updates during conversation. You never open the file
directly. You never manually edit a status field. The agent does this as
a natural side effect of talking with you.

Here is what this looks like in practice:

**You say:** "I finished the beta testing milestone for Project Alpha."

**The agent:**
1. Updates the project tracker — changes status, advances progress bar
2. Updates the weekly dashboard — moves it from "in progress" to "done"
3. Updates the goals dashboard — advances the relevant key result
4. Logs it as a win in this week's review

You did not ask for any of this. The agent understood the implication of
what you said and kept your system current.

## How SAME Powers This

Each SAME feature maps directly to a dashboard capability:

### Session Context (`get_session_context`)
At the start of every session, the agent reads your dashboards as part of
orientation. This means the agent always knows the current state of your
projects, goals, energy patterns, and pending decisions — without you
having to explain anything.

### Note Saving (`save_note`)
When something changes during conversation, the agent writes the update
directly to the relevant dashboard file. The dashboard is just a markdown
file in the vault — the agent has full read/write access.

### Handoffs (`create_handoff`)
When a session ends, the agent can note what changed. The next session
picks up with accurate context. Dashboards stay current across sessions
even when you switch topics entirely.

### Search (`search_notes`)
When you ask about a project or decision, the agent can pull context from
the relevant dashboard instantly. "What did we decide about the hosting
provider?" triggers a search that finds the decision dashboard entry.

## The Five Core Dashboards

| Dashboard          | Purpose                    | Update Trigger                     |
|--------------------|----------------------------|------------------------------------|
| Weekly Dashboard   | Week-at-a-glance           | Monday setup, Friday review        |
| Project Tracker    | All projects, all statuses | Any project status change          |
| Energy Map         | When you work best         | Agent asks periodically            |
| Goals Progress     | Quarterly OKR tracking     | Weekly review, milestone updates   |
| Decision Dashboard | Decisions made and pending | Any significant decision           |

## Why Markdown

Dashboards are plain markdown files because:

- **No vendor lock-in.** They work in any text editor, any tool.
- **Version controlled.** Git tracks every change. You can see how your
  projects evolved over time.
- **Agent-friendly.** LLMs read and write markdown natively. No API
  adapters, no format conversion.
- **Human-readable.** When you do want to look at a dashboard directly,
  it renders beautifully in any markdown viewer.

## The Zero-Maintenance Promise

The goal is that you never think about your tracking system. You have
conversations with your agent. You make decisions, complete tasks, change
priorities. The dashboards update themselves as a consequence. When you
need a status update, you ask — and the answer is always current.

This is what separates a living dashboard from a dead spreadsheet.

See: `templates/dashboards/`, `hub/systems.md`
