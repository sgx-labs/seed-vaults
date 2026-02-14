---
title: "The Permission System in Depth"
tags: [permissions, settings, security, configuration, project-setup, allow-deny, wildcard]
content_type: research
domain: engineering
---

# The Permission System in Depth

## How Permissions Work

Claude Code asks for your approval before running any tool (Bash commands, file edits, MCP tool calls). The permission system lets you pre-approve or pre-deny specific actions so you are not prompted every time.

When Claude wants to run a tool:
1. Check deny rules first. If any deny rule matches, the action is blocked.
2. Check allow rules. If an allow rule matches, the action runs without prompting.
3. If no rule matches, Claude shows an approval prompt and you decide.

## Three Configuration Levels

Permissions live in three files, each with a different scope:

| Level | File | Scope | In Git? |
|-------|------|-------|---------|
| Global | `~/.claude/settings.json` | All projects on this machine | No |
| Project | `.claude/settings.json` | This repository | Yes |
| Personal | `.claude/settings.local.json` | This repo, this machine only | No (gitignored) |

### Global Settings (`~/.claude/settings.json`)

Apply to every project. Good for personal MCP servers and universal safety rules.

```json
{
  "permissions": {
    "allow": [
      "mcp__same__search_notes",
      "mcp__same__get_session_context"
    ],
    "deny": [
      "Bash(rm -rf /)",
      "Bash(sudo *)"
    ]
  }
}
```

### Project Settings (`.claude/settings.json`)

Checked into git. Shared with the whole team. Define project-specific commands and safety rules.

```json
{
  "permissions": {
    "allow": [
      "Bash(npm test)",
      "Bash(npm run build)",
      "Bash(npm run lint)",
      "Bash(npx prisma *)"
    ],
    "deny": [
      "Bash(npm publish *)",
      "Bash(rm -rf *)",
      "Bash(git push --force *)"
    ]
  }
}
```

### Personal Settings (`.claude/settings.local.json`)

Gitignored. Overrides or extends project settings for your specific machine. Use for personal tooling, local paths, or editor-specific commands.

```json
{
  "permissions": {
    "allow": [
      "Bash(docker compose up *)",
      "Bash(open http://localhost:*)"
    ]
  }
}
```

## Permission Patterns

### Exact Match

Match a specific command exactly as Claude would run it.

```json
"allow": ["Bash(npm test)"]
```

This allows `npm test` but NOT `npm test -- --watch` or `npm test src/`.

### Wildcard Patterns

Use `*` to match any suffix.

```json
"allow": [
  "Bash(npm run *)",
  "Bash(go test *)",
  "Bash(git diff *)",
  "Bash(git log *)"
]
```

`Bash(npm run *)` allows `npm run build`, `npm run test`, `npm run lint:fix`, etc.

### Tool-Specific Permissions

Each tool type has its own permission format:

| Tool | Format | Example |
|------|--------|---------|
| Bash commands | `Bash(command)` | `Bash(make build)` |
| File edits | `Edit(path pattern)` | `Edit(src/**)` |
| MCP tools | `mcp__server__tool` | `mcp__same__search_notes` |
| Read files | `Read(path pattern)` | `Read(src/**)` |
| Write files | `Write(path pattern)` | `Write(src/**)` |

### File Path Patterns

For Edit, Read, and Write permissions, use glob patterns:

```json
{
  "permissions": {
    "allow": [
      "Edit(src/**)",
      "Edit(tests/**)",
      "Read(docs/**)"
    ],
    "deny": [
      "Edit(.env*)",
      "Edit(*.pem)",
      "Write(dist/**)"
    ]
  }
}
```

`**` matches any depth of subdirectories. `*` matches within a single directory level.

### MCP Tool Permissions

MCP tools follow the pattern `mcp__<server-name>__<tool-name>`:

```json
{
  "permissions": {
    "allow": [
      "mcp__same__search_notes",
      "mcp__same__save_note",
      "mcp__same__get_session_context",
      "mcp__github__create_issue"
    ]
  }
}
```

## How Permissions Merge Across Levels

When all three config files exist, they merge with these rules:

1. **Deny rules from ANY level apply.** If global denies `Bash(rm -rf *)`, it is denied everywhere, even if the project allows it.
2. **Allow rules are additive.** Global allows + project allows + personal allows are all active.
3. **Deny always wins over allow.** If the same pattern appears in both allow and deny, deny takes precedence.

### Example: Merge in Action

Global (`~/.claude/settings.json`):
```json
{ "permissions": { "deny": ["Bash(sudo *)"] } }
```

Project (`.claude/settings.json`):
```json
{ "permissions": { "allow": ["Bash(npm test)"], "deny": ["Bash(npm publish *)"] } }
```

Personal (`.claude/settings.local.json`):
```json
{ "permissions": { "allow": ["Bash(docker compose *)"] } }
```

Result:
- `npm test` -- allowed (project allow)
- `docker compose up` -- allowed (personal allow)
- `npm publish --tag latest` -- denied (project deny)
- `sudo apt install` -- denied (global deny)
- `curl https://example.com` -- prompts for approval (no matching rule)

## The Approval Prompt

When Claude tries an action with no matching allow or deny rule, you see a prompt:

```
Claude wants to run: npm install express

  Allow   Allow Always   Deny
```

- **Allow** -- permits this one instance
- **Allow Always** -- adds the command to your personal settings (`.claude/settings.local.json`) so it never asks again
- **Deny** -- blocks this instance

"Allow Always" is the fastest way to build up your permission set organically. As you work, you approve commands you trust, and they get saved.

## Pre-Approving Common Commands for Speed

A well-configured project settings file eliminates most approval prompts. Here is a starter set for common project types.

### Node.js / TypeScript Project

```json
{
  "permissions": {
    "allow": [
      "Bash(npm test *)",
      "Bash(npm run *)",
      "Bash(npx *)",
      "Bash(node *)",
      "Bash(tsc *)",
      "Bash(git status *)",
      "Bash(git diff *)",
      "Bash(git log *)",
      "Bash(git add *)",
      "Bash(git commit *)"
    ],
    "deny": [
      "Bash(npm publish *)",
      "Bash(git push --force *)",
      "Bash(rm -rf *)"
    ]
  }
}
```

### Go Project

```json
{
  "permissions": {
    "allow": [
      "Bash(go test *)",
      "Bash(go build *)",
      "Bash(go vet *)",
      "Bash(go run *)",
      "Bash(make *)",
      "Bash(git status *)",
      "Bash(git diff *)",
      "Bash(git log *)"
    ],
    "deny": [
      "Bash(go install *)",
      "Bash(git push --force *)",
      "Bash(rm -rf *)"
    ]
  }
}
```

### Python Project

```json
{
  "permissions": {
    "allow": [
      "Bash(python *)",
      "Bash(pytest *)",
      "Bash(pip install *)",
      "Bash(ruff *)",
      "Bash(mypy *)",
      "Bash(git status *)",
      "Bash(git diff *)",
      "Bash(git log *)"
    ],
    "deny": [
      "Bash(pip install --user *)",
      "Bash(twine upload *)",
      "Bash(git push --force *)"
    ]
  }
}
```

## Tips

- **Start permissive, tighten later.** Pre-approve your build/test/lint commands on day one. Add deny rules when you discover dangerous patterns.
- **Deny is your safety net.** Even if you accidentally allow something broad, a matching deny rule overrides it.
- **Keep project settings minimal.** Only pre-approve commands the whole team uses. Let individuals add personal preferences in `.claude/settings.local.json`.
- **Review your settings periodically.** Run `cat .claude/settings.json` to see what is approved and prune anything stale.
