---
title: "Hooks System — Current State"
tags: [hooks, lifecycle, entity, events, automation]
content_type: entity
domain: engineering
---

# Hooks System — Current State

## What It Is
Shell commands that run automatically at specific points in the Claude Code agent lifecycle. Defined in settings.json.

## Available Hook Points

| Hook | Trigger | Guaranteed? | Input |
|------|---------|-------------|-------|
| `SessionStart` | Session begins | Yes | Session metadata |
| `Stop` | Session ends | Best-effort | Session summary |
| `UserPromptSubmit` | User sends message | Yes | User's message text |
| `PreToolUse` | Before tool executes | Yes | Tool name + parameters |
| `PostToolUse` | After tool executes | Yes | Tool name + result |
| `Notification` | Agent notification | Yes | Notification content |
| `SubagentStop` | Subagent completes | Yes | Subagent result |

## Execution Model
- Hooks run as shell commands (any executable)
- stdout is injected into conversation context
- stderr is logged but not shown to agent
- Non-zero exit code = hook failed (agent sees error)
- Multiple hooks per event run in order
- One hook failure doesn't block others

## Configuration

```json
{
  "hooks": {
    "SessionStart": [
      {
        "type": "command",
        "command": "your-command --args"
      }
    ]
  }
}
```

## Design Constraints
- **SessionStart is the only guaranteed hook** — Stop may not fire if session crashes
- **Don't put critical logic in Stop** — treat it as best-effort cleanup
- **Hook output counts against context** — keep output concise
- **Hooks run synchronously** — long hooks delay the session

## SAME Hook Integration
SAME uses 6 hooks: session-start, session-bootstrap, user-prompt, stop, pre-tool-use, notification.

## Last Updated
February 2026
