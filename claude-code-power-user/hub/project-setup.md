---
title: "Project Setup — CLAUDE.md, Settings, Permissions"
tags: [project-setup, claude-md, settings, permissions, configuration]
content_type: hub
domain: engineering
---

# Project Setup — CLAUDE.md, Settings, Permissions

## Overview

Project setup determines how Claude Code behaves in your repository. Three files control everything: CLAUDE.md (instructions), settings.json (permissions), and .claude/ directory (state).

## CLAUDE.md

The most important file. Claude reads it at the start of every session and follows its instructions.

```markdown
# My Project

## Build & Test
- `npm run build` to build
- `npm test` to run tests
- `npm run lint` to lint

## Rules
- Always write tests for new features
- Use TypeScript strict mode
- Never commit directly to main

## Architecture
- src/api/ — Express route handlers
- src/services/ — Business logic
- src/models/ — Database models
```

**What to include:**
- Build/test/lint commands
- Project conventions and rules
- Architecture overview
- Things Claude should never do
- Git workflow preferences

**What not to include:**
- Secrets or API keys
- Personal information
- Redundant information Claude can discover itself

See: `research/setup/claude-md-guide.md`

## Settings

### .claude/settings.json (Project, checked into git)
```json
{
  "permissions": {
    "allow": [
      "Bash(npm run test)",
      "Bash(npm run build)"
    ],
    "deny": [
      "Bash(rm -rf *)",
      "Bash(git push --force)"
    ]
  },
  "hooks": {
    "SessionStart": [...]
  },
  "mcpServers": {
    "same": { ... }
  }
}
```

### .claude/settings.local.json (Personal, gitignored)
For personal preferences that shouldn't be shared with the team.

### ~/.claude/settings.json (Global)
Applies to all projects. Good for global MCP servers and personal preferences.

## Permission System

Claude asks for approval before running tools. You can pre-approve patterns:

| Permission | Example |
|-----------|---------|
| Allow specific commands | `Bash(npm test)` |
| Allow command patterns | `Bash(npm run *)` |
| Deny dangerous commands | `Bash(rm -rf *)` |
| Allow file edits | `Edit(src/**)` |
| Allow MCP tools | `mcp__same__search_notes` |

## Research Notes

- `research/setup/claude-md-guide.md` — Writing effective CLAUDE.md files
- `research/setup/permissions-settings.md` — Permission system in depth
- `research/setup/team-setup.md` — Setting up Claude Code for a team
- `research/setup/first-project-setup.md` — Step-by-step first project guide
- `research/setup/settings-hierarchy.md` — How settings merge and override
