---
title: "Getting Started with Claude Code"
tags: [getting-started, install, setup, beginner]
content_type: hub
domain: engineering
---

# Getting Started with Claude Code

## What Is Claude Code?

Claude Code is Anthropic's CLI-based AI coding agent. It runs in your terminal, reads and writes files, executes commands, and helps you build software. It understands your entire codebase and can make changes across multiple files.

## Installation

```bash
# Recommended: npm (requires Node.js 18+)
npm install -g @anthropic-ai/claude-code

# Verify
claude --version
```

## First Project

```bash
cd your-project
claude
```

That's it. Claude Code reads your codebase and you start talking to it. On first run, it scans the project structure and creates a `.claude/` directory for settings.

## Key Files

- **CLAUDE.md** — Project instructions Claude follows every session. See `research/setup/claude-md-guide.md`
- **.claude/settings.json** — Permissions, allowed tools, denied patterns
- **.claude/settings.local.json** — Your personal settings (gitignored)

## Essential Concepts

| Concept | What it does | Learn more |
|---------|-------------|------------|
| CLAUDE.md | Project governance | `hub/project-setup.md` |
| Hooks | Lifecycle automation | `hub/hooks.md` |
| MCP Servers | External tools | `hub/mcp-servers.md` |
| Slash commands | Built-in shortcuts | `research/workflows/slash-commands.md` |
| Plan mode | Think before coding | `research/workflows/plan-mode.md` |
| Subagents | Parallel workers | `research/advanced/subagents.md` |

## First Things to Try

1. **Ask about your codebase** — "What does this project do?"
2. **Fix a bug** — "Fix the failing test in auth.test.ts"
3. **Add a feature** — "Add input validation to the signup form"
4. **Code review** — "Review the changes in the last commit"
5. **Git operations** — "Create a commit for the auth changes"

## Research Notes

- `research/setup/first-project-setup.md` — Detailed first project walkthrough
- `research/setup/claude-md-guide.md` — Writing effective CLAUDE.md files
- `research/setup/permissions-settings.md` — Permission system explained
- `research/workflows/daily-workflow.md` — How to structure your day with Claude Code
