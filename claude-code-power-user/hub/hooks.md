---
title: "Hooks — Lifecycle Automation"
tags: [hooks, lifecycle, automation, SessionStart, events]
content_type: hub
domain: engineering
---

# Hooks — Lifecycle Automation

## What Are Hooks?

Hooks are shell commands that run automatically at specific points in the Claude Code lifecycle. They let you automate tasks like loading context at session start, saving work at session end, or validating tool usage.

## Hook Points

| Hook | When it fires | Common use |
|------|--------------|------------|
| `SessionStart` | Agent session begins | Load context, check environment |
| `Stop` | Session ends (best-effort) | Save handoff notes, clean up |
| `UserPromptSubmit` | User sends a message | Surface relevant notes, validate input |
| `PreToolUse` | Before a tool executes | Block dangerous operations |
| `PostToolUse` | After a tool executes | Log actions, trigger side effects |
| `Notification` | Agent sends a notification | Custom notification routing |
| `SubagentStop` | Subagent completes | Aggregate subagent results |

## Configuration

Hooks are defined in `.claude/settings.json` (project-level) or `~/.claude/settings.json` (global):

```json
{
  "hooks": {
    "SessionStart": [
      {
        "type": "command",
        "command": "same hook session-start"
      }
    ],
    "UserPromptSubmit": [
      {
        "type": "command",
        "command": "same hook user-prompt"
      }
    ]
  }
}
```

## Key Concepts

- **Hooks run as shell commands** — Any executable works (bash, python, node, compiled binary)
- **SessionStart is guaranteed** — It always fires when a session begins
- **Stop is best-effort** — Don't put critical logic in Stop hooks (the session might crash)
- **Hook output becomes context** — Whatever your hook prints to stdout is injected into the conversation
- **Multiple hooks per event** — They run in order; if one fails, the rest still run

## Common Patterns

### Context Surfacing (UserPromptSubmit)
Automatically find relevant notes for every user message:
```bash
same hook user-prompt  # SAME surfaces relevant knowledge base notes
```

### Session Orientation (SessionStart)
Load project state at the start of every session:
```bash
same hook session-start  # SAME loads pinned notes + recent handoffs
```

### Guard Rails (PreToolUse)
Block dangerous operations:
```bash
same hook pre-tool-use  # SAME checks push protection rules
```

## Research Notes

- `research/hooks/hook-lifecycle.md` — Detailed lifecycle and execution model
- `research/hooks/writing-custom-hooks.md` — Building your own hooks
- `research/hooks/same-hooks.md` — How SAME uses hooks for persistent memory
- `research/hooks/hook-debugging.md` — Debugging hooks when they don't work
- `research/hooks/pretooluse-patterns.md` — PreToolUse guard patterns
