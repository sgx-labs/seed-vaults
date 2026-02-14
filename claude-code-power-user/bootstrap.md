---
title: "Bootstrap — New Session Quick Start"
tags: [bootstrap, onboarding, session-start, critical, water-the-seed, research-cascade, orientation]
content_type: hub
domain: engineering
pinned: true
---

# Bootstrap — New Session Quick Start

## Water the Seed

First time? Your agent will handle everything. Just run:

```bash
same init && same reindex
```

Then start a conversation. Your agent reads this file automatically and begins the Research Cascade — a multi-phase process that scans your project, understands your needs, asks smart questions, and fills your vault with personalized knowledge. The more you interact, the smarter it gets.

**What happens next (automatic):**
1. Agent silently scans your project — tech stack, config, git history, existing docs
2. Agent presents an insight report showing what it learned about your project
3. Agent asks 3-5 targeted questions about your workflow and pain points
4. Agent proposes CLAUDE.md additions (archives originals safely)
5. Agent recommends research topics specific to YOUR project
6. You pick which topics matter — agents research in parallel, save results to your vault
7. Each research result suggests new topics — the vault grows exponentially

After the first session, your vault has generic Claude Code knowledge PLUS project-specific intelligence that no one else has.

## Returning Sessions

Already set up? Here's what your agent does automatically on every session:

- **Loads context** — SAME surfaces your pinned notes, recent handoffs, and decisions
- **Remembers where you left off** — session handoffs capture what was done and what's pending
- **Searches before generating** — your vault has 50+ expert notes, growing with every session
- **Cross-references** — finds related notes you might not know about
- **Compounds knowledge** — every useful answer gets saved for next time

## Where To Find Things

| Need | Search for | Or read directly |
|------|-----------|-----------------|
| Getting started | `install setup first project` | `hub/getting-started.md` |
| Daily workflows | `workflow efficiency patterns` | `hub/workflows.md` |
| Hooks | `hooks SessionStart lifecycle` | `hub/hooks.md` |
| MCP servers | `MCP server install configure` | `hub/mcp-servers.md` |
| Prompt patterns | `prompt effective coding` | `hub/prompt-patterns.md` |
| Project setup | `CLAUDE.md permissions settings` | `hub/project-setup.md` |
| Troubleshooting | `error debug fix common` | `hub/troubleshooting.md` |
| Model selection | `Opus Sonnet Haiku when use` | `entities/claude-code.md` |
| Context management | `tokens context window manage` | `entities/context-window.md` |

## Key Questions This Seed Answers

1. How do I set up a new project with Claude Code?
2. What goes in CLAUDE.md and why does it matter?
3. How do hooks work (SessionStart, Stop, PreToolUse, etc.)?
4. What are the best MCP servers to install?
5. How do I write effective prompts for different task types?
6. What are slash commands and how do I create custom ones?
7. How do I manage permissions and tool approval?
8. What keyboard shortcuts speed up my workflow?
9. How do I use Claude Code with git effectively?
10. How do I handle large codebases?
11. What are common mistakes that waste tokens?
12. How do I use plan mode vs direct execution?
13. How do I set up multi-file editing workflows?
14. What's the difference between Opus, Sonnet, and Haiku?
15. How do I use Claude Code for debugging?
16. How do I integrate with VS Code, Cursor, or other editors?
17. How do I use subagents for parallel work?
18. What are best practices for CLAUDE.md governance?
19. How do I use Claude Code for code review?
20. How do I batch operations to reduce token usage?

## Content Organization

- `hub/` — Category overviews, start here for any topic
- `research/` — Detailed guides, one question per file (grows with each session)
- `research/project-specific/` — Notes tailored to YOUR project (created during cascade)
- `entities/` — Living docs about tools/concepts that change over time
- `decisions/` — Architectural decisions for your project
- `sessions/` — Session handoffs for continuity
- `_archive/` — Safely archived originals from merge process
