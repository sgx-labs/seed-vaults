---
title: "How Settings Merge and Override"
tags: [settings, configuration, permissions, merge, hierarchy]
content_type: research
domain: engineering
---

# How Settings Merge and Override

## The Three Config Files

Claude Code reads settings from three locations, in this order:

| Priority | File | Scope | Shared? |
|----------|------|-------|---------|
| 1 (lowest) | `~/.claude/settings.json` | Global (all projects) | No |
| 2 | `.claude/settings.json` | Project (this repo) | Yes (git) |
| 3 (highest) | `.claude/settings.local.json` | Personal (this repo, this machine) | No (gitignored) |

All three are optional. Claude works fine with none of them. Each adds or overrides behavior from the levels below it.

## What Each File Is For

### Global: `~/.claude/settings.json`

Applies to every project on your machine. Use it for:

- Personal MCP servers you always want available (SAME, note-taking tools)
- Universal deny rules (never run `sudo`, never delete root directories)
- Personal preferences that apply everywhere

```json
{
  "permissions": {
    "allow": [
      "mcp__same__search_notes",
      "mcp__same__get_session_context",
      "mcp__same__save_note"
    ],
    "deny": [
      "Bash(sudo *)",
      "Bash(rm -rf /)",
      "Bash(chmod 777 *)"
    ]
  },
  "mcpServers": {
    "same": {
      "type": "stdio",
      "command": "same",
      "args": ["mcp"],
      "env": {}
    }
  }
}
```

### Project: `.claude/settings.json`

Checked into git. Shared with the entire team. Use it for:

- Build, test, and lint command permissions
- Project-specific deny rules (no force push, no publish)
- Shared hooks (SessionStart, Stop)
- Team MCP servers

```json
{
  "permissions": {
    "allow": [
      "Bash(npm test *)",
      "Bash(npm run *)",
      "Bash(git status *)",
      "Bash(git diff *)",
      "Bash(git log *)",
      "Bash(git add *)",
      "Bash(git commit *)"
    ],
    "deny": [
      "Bash(npm publish *)",
      "Bash(git push --force *)",
      "Bash(rm -rf *)",
      "Edit(.env*)"
    ]
  },
  "hooks": {
    "SessionStart": [
      {
        "type": "command",
        "command": "same hook session-start"
      }
    ]
  }
}
```

### Personal: `.claude/settings.local.json`

Gitignored. Your overrides for this specific repo on this specific machine. Use it for:

- Commands only you use (Docker, editor commands, local scripts)
- Personal MCP servers for this project
- Overriding project settings for your workflow

```json
{
  "permissions": {
    "allow": [
      "Bash(docker compose *)",
      "Bash(open http://localhost:*)",
      "Bash(./scripts/local-setup.sh)"
    ]
  }
}
```

## How Permissions Merge

### Allow Rules: Additive

Allow rules from all three levels are combined. If global allows `mcp__same__search_notes` and project allows `Bash(npm test *)`, both are active in the session.

```
Global allows:   [A, B]
Project allows:  [C, D]
Personal allows: [E]

Effective allows: [A, B, C, D, E]
```

### Deny Rules: Additive, and Deny Always Wins

Deny rules from all three levels are also combined. But critically, if a pattern appears in both allow and deny, **deny wins**.

```
Global denies:   [X]
Project denies:  [Y, Z]
Personal denies: []

Effective denies: [X, Y, Z]
```

If project allows `Bash(npm run *)` and global denies `Bash(npm run deploy-prod)`, the deploy-prod command is denied even though it matches the project allow pattern.

### The Full Resolution Order

When Claude wants to run a tool:

```
1. Collect all deny rules from global + project + personal
2. If the action matches ANY deny rule → BLOCKED
3. Collect all allow rules from global + project + personal
4. If the action matches ANY allow rule → ALLOWED
5. If no rule matches → PROMPT the user for approval
```

## How Hooks Merge

Hooks are **additive** across levels. If global defines a SessionStart hook and project defines a different SessionStart hook, both run.

Global:
```json
{
  "hooks": {
    "SessionStart": [
      { "type": "command", "command": "echo 'global start'" }
    ]
  }
}
```

Project:
```json
{
  "hooks": {
    "SessionStart": [
      { "type": "command", "command": "same hook session-start" }
    ]
  }
}
```

Both hooks fire on session start. Global hooks run first, then project hooks.

## How MCP Servers Merge

MCP servers from all levels are combined by name. If the same server name appears at multiple levels, the higher-priority level wins.

Global:
```json
{
  "mcpServers": {
    "same": { "command": "same", "args": ["mcp"] },
    "notes": { "command": "notes-server", "args": [] }
  }
}
```

Project:
```json
{
  "mcpServers": {
    "github": { "command": "gh-mcp", "args": [] }
  }
}
```

Personal:
```json
{
  "mcpServers": {
    "same": { "command": "same", "args": ["mcp", "--vault", "/custom/path"] }
  }
}
```

Effective servers:
- `same` -- uses **personal** config (overrides global)
- `notes` -- uses global config
- `github` -- uses project config

## Practical Scenarios

### Scenario 1: Team Safety + Personal Convenience

The team wants to prevent force pushes and accidental deletions. You personally use Docker frequently.

Project (`.claude/settings.json`):
```json
{
  "permissions": {
    "deny": ["Bash(git push --force *)", "Bash(rm -rf *)"]
  }
}
```

Personal (`.claude/settings.local.json`):
```json
{
  "permissions": {
    "allow": ["Bash(docker *)"]
  }
}
```

Result: Docker commands run without prompting. Force push and `rm -rf` are always blocked.

### Scenario 2: Override a Project MCP Server Locally

The project configures a shared MCP server with a team vault path. You want to point it at your personal vault instead.

Project:
```json
{
  "mcpServers": {
    "same": { "command": "same", "args": ["mcp", "--vault", "/team/shared-vault"] }
  }
}
```

Personal:
```json
{
  "mcpServers": {
    "same": { "command": "same", "args": ["mcp", "--vault", "/my/personal-vault"] }
  }
}
```

Your personal config wins for the `same` server. Other team members still get the team vault.

### Scenario 3: Global Safety Net

You want certain commands blocked in every project, without relying on each project to set them up.

Global (`~/.claude/settings.json`):
```json
{
  "permissions": {
    "deny": [
      "Bash(sudo *)",
      "Bash(rm -rf /)",
      "Bash(chmod 777 *)",
      "Bash(curl * | sh)",
      "Bash(wget * | sh)",
      "Edit(*.pem)",
      "Edit(*.key)"
    ]
  }
}
```

These deny rules apply to every project, even those with no `.claude/settings.json`.

### Scenario 4: Personal Allow Does Not Override Team Deny

A developer tries to allow a command the team has denied:

Project (`.claude/settings.json`):
```json
{
  "permissions": {
    "deny": ["Bash(npm publish *)"]
  }
}
```

Personal (`.claude/settings.local.json`):
```json
{
  "permissions": {
    "allow": ["Bash(npm publish *)"]
  }
}
```

Result: `npm publish` is **still denied**. Deny always wins, regardless of which level the allow comes from. This ensures team safety rules cannot be bypassed by individual settings.

## Quick Reference

```
PERMISSIONS:
  allow → additive (all levels combined)
  deny  → additive (all levels combined)
  deny wins over allow (always)

HOOKS:
  additive (all levels run, global first)

MCP SERVERS:
  merged by name (higher priority level wins)
  Priority: personal > project > global

FILES:
  ~/.claude/settings.json           Global (all projects)
  .claude/settings.json             Project (shared via git)
  .claude/settings.local.json       Personal (gitignored)
```

## Debugging Settings Issues

If Claude is not behaving as expected, check which settings are active:

```
You: "Read ~/.claude/settings.json, .claude/settings.json, and
      .claude/settings.local.json and show me the effective merged
      permissions."
```

Common issues:
- **Command still prompts**: The allow pattern does not match exactly. Check wildcards.
- **Command blocked unexpectedly**: A deny rule at a higher level is matching. Check global settings.
- **MCP server not loading**: Server name collision between levels. Check for duplicate names.
- **Hook not firing**: Verify the command path is correct and the executable has permissions.
