---
title: "Setting Up Claude Code for a Team"
tags: [team, collaboration, onboarding, settings, project-setup]
content_type: research
domain: engineering
---

# Setting Up Claude Code for a Team

## The Goal

Every developer on the team should get consistent behavior from Claude Code: same build commands, same safety rules, same conventions. Individual preferences (editor, local tools, personal MCP servers) remain personal.

## Shared vs Personal Configuration

| File | Shared? | Purpose |
|------|---------|---------|
| `CLAUDE.md` | Yes (in git) | Project instructions, conventions, architecture |
| `.claude/settings.json` | Yes (in git) | Permissions, hooks, MCP servers |
| `.claude/settings.local.json` | No (gitignored) | Personal overrides, local paths |
| `~/.claude/settings.json` | No (per machine) | Global personal preferences |

## Step 1: Write a Shared CLAUDE.md

This is the most important step. The CLAUDE.md should contain everything a new developer (or Claude) needs to know about the project.

```markdown
# Project Name

## Build & Test
- `make build` — compile
- `make test` — run all tests
- `make lint` — run linters

## Architecture
- cmd/ — CLI entry points
- internal/ — Private packages
- pkg/ — Public API
- tests/ — Integration tests

## Conventions
- All errors wrapped with context: fmt.Errorf("op: %w", err)
- Table-driven tests with subtests
- No exported functions without doc comments

## Rules
- NEVER push to main directly
- NEVER commit generated files
- Always run `make test` before committing
- PR reviews required before merge

## Git Workflow
- Branch from main: feature/description or fix/description
- Conventional commits: feat:, fix:, docs:, chore:
- Squash merge to main
```

Commit this to the repository root. Every team member gets it automatically.

## Step 2: Set Up Shared Permissions

Create `.claude/settings.json` with commands the whole team uses:

```json
{
  "permissions": {
    "allow": [
      "Bash(make *)",
      "Bash(go test *)",
      "Bash(go build *)",
      "Bash(go vet *)",
      "Bash(git status *)",
      "Bash(git diff *)",
      "Bash(git log *)",
      "Bash(git add *)",
      "Bash(git commit *)"
    ],
    "deny": [
      "Bash(git push --force *)",
      "Bash(rm -rf *)",
      "Bash(go install *)"
    ]
  }
}
```

Check this into git. The team agrees on what Claude can and cannot do.

## Step 3: Add Shared Hooks (Optional)

Hooks automate actions at specific points in the Claude Code lifecycle. Define them in `.claude/settings.json`:

```json
{
  "hooks": {
    "SessionStart": [
      {
        "type": "command",
        "command": "same hook session-start"
      }
    ],
    "Stop": [
      {
        "type": "command",
        "command": "same hook session-stop"
      }
    ]
  }
}
```

Common team hooks:
- **SessionStart**: Load project context, check for handoff notes, run `git fetch`
- **Stop**: Create handoff notes, save session summary
- **PreToolUse**: Custom validation before tool execution
- **PostToolUse**: Logging, audit trail

## Step 4: Configure Shared MCP Servers (Optional)

MCP servers give Claude access to external tools. Define shared ones in `.claude/settings.json`:

```json
{
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

Common team MCP servers:
- **SAME** -- knowledge base search and session memory
- **GitHub** -- issue and PR management
- **Database** -- read-only query access
- **Documentation** -- internal docs search

## Step 5: Set Up .gitignore

Make sure personal settings are not accidentally committed:

```gitignore
# Claude Code personal settings
.claude/settings.local.json
```

The `.claude/` directory itself should NOT be gitignored -- `settings.json` inside it is shared.

## Step 6: Personal Settings

Each developer creates their own `.claude/settings.local.json` for personal preferences:

```json
{
  "permissions": {
    "allow": [
      "Bash(docker compose *)",
      "Bash(open http://localhost:*)",
      "Bash(code *)"
    ]
  },
  "mcpServers": {
    "personal-notes": {
      "type": "stdio",
      "command": "same",
      "args": ["mcp", "--vault", "/path/to/my/notes"],
      "env": {}
    }
  }
}
```

This file is gitignored, so it stays on their machine only.

## Onboarding a New Team Member

### Checklist

1. **Install Claude Code** -- `npm install -g @anthropic-ai/claude-code`
2. **Clone the repository** -- shared CLAUDE.md and `.claude/settings.json` are already there
3. **Run `claude` in the project directory** -- Claude reads CLAUDE.md automatically
4. **Create `.claude/settings.local.json`** -- add personal preferences
5. **Install shared tools** -- any MCP servers or hooks referenced in settings.json
6. **First task: ask Claude to explain the project** -- validates that CLAUDE.md is working

### Onboarding Prompt

A good first prompt for a new team member:

```
Read CLAUDE.md and give me a summary of this project: what it does,
how to build it, what the main conventions are, and what I should
avoid doing.
```

If Claude gives a clear, accurate answer, the CLAUDE.md is working.

## Team Patterns

### Code Review Conventions

Add to CLAUDE.md:

```markdown
## Code Review
- Use `gh pr view <number>` to read PRs
- Focus on: error handling, test coverage, naming, security
- Flag but don't fix style issues (linter handles those)
```

### Consistent Commit Messages

```markdown
## Commits
- Format: type(scope): description
- Types: feat, fix, docs, test, chore, refactor
- Scope: package or feature area
- Example: feat(auth): add JWT refresh token rotation
```

### Shared Deny Rules

Agree as a team on what Claude should never do:

```json
{
  "permissions": {
    "deny": [
      "Bash(git push --force *)",
      "Bash(rm -rf *)",
      "Bash(DROP TABLE *)",
      "Bash(kubectl delete *)",
      "Edit(.env*)",
      "Edit(*.pem)",
      "Edit(*.key)"
    ]
  }
}
```

### Environment-Specific Rules

If your project has staging and production environments, add explicit rules:

```markdown
## Environments
- NEVER run commands against production from Claude Code
- Local dev: localhost:3000 (frontend), localhost:8080 (API)
- Test DB: use `make db:test:reset` to reset test database
- Staging: deploy via CI only, never manual
```

## Common Team Pitfalls

- **No CLAUDE.md** -- every developer's Claude behaves differently. Fix: write one together as a team exercise.
- **CLAUDE.md too personal** -- one developer's preferences imposed on everyone. Fix: keep personal preferences in `.claude/settings.local.json`.
- **Overly permissive settings** -- allowing `Bash(*)` defeats the purpose. Fix: allow specific commands, deny dangerous ones.
- **No deny rules** -- relying on developers to always click "Deny" for dangerous actions. Fix: explicitly deny destructive commands in shared settings.
- **Forgetting to update** -- CLAUDE.md drifts from reality. Fix: review it in sprint retros or when onboarding reveals gaps.

## Quick Reference

```
Shared (git):
  CLAUDE.md                    # Instructions for Claude
  .claude/settings.json        # Permissions, hooks, MCP servers

Personal (gitignored):
  .claude/settings.local.json  # Your overrides

Global (per machine):
  ~/.claude/settings.json      # All-project preferences
  ~/.claude/CLAUDE.md          # All-project instructions
```
