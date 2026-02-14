---
title: "CLAUDE.md — Current State"
tags: [claude-md, governance, entity, configuration, project-setup, instructions]
content_type: entity
pinned: true
domain: engineering
---

# CLAUDE.md — Current State

## What It Is
A markdown file in your project root that Claude Code reads at the start of every session. It contains project-specific instructions, conventions, and rules.

## How It Works
- Claude reads CLAUDE.md automatically when starting a session in a directory
- Instructions in CLAUDE.md override default behavior
- It's version-controlled — check it into git for team consistency
- Nested CLAUDE.md files in subdirectories add context for that subtree

## File Hierarchy
1. `~/.claude/CLAUDE.md` — Global (all projects)
2. `./CLAUDE.md` — Project root (this project)
3. `./src/CLAUDE.md` — Subdirectory (adds context when working in src/)

Lower files add to (don't replace) higher ones.

## What to Include
- Build, test, and lint commands
- Project architecture overview
- Coding conventions and patterns
- Things Claude should never do
- Git workflow preferences
- Key file paths and what they contain

## What NOT to Include
- Secrets, API keys, passwords
- Personal information
- Information Claude can discover by reading code
- Extremely long documents (keep it scannable)

## Best Practices
- Keep it under 200 lines for the root file
- Use headers and bullet points (scannable)
- Put "never do X" rules near the top
- Include the commands Claude needs most (build, test)
- Update it as the project evolves

## Related
- `.claude/settings.json` — permissions and tool configuration
- `.claude/settings.local.json` — personal settings (gitignored)

## Last Updated
February 2026
