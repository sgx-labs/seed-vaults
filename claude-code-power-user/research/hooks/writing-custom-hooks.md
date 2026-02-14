---
title: "Writing Custom Hooks"
tags: [hooks, custom-hooks, bash, python, settings, tutorial, stdin-stdout, json]
content_type: research
domain: engineering
---

# Writing Custom Hooks

## The Question

How do I build my own hooks from scratch, configure them, and test them?

## A Hook Is Just a Shell Command

Claude Code hooks are not a special API. A hook is any executable command that:

1. Reads JSON from stdin (optional — some hooks ignore it)
2. Does its work
3. Prints JSON to stdout (so the agent gets the context)
4. Exits with code 0 (success) or non-zero (failure/block)

Bash scripts, Python scripts, Node scripts, Go binaries, Rust binaries — anything that runs in a shell works.

## Configuration

Hooks live in `.claude/settings.json` (project-level) or `~/.claude/settings.json` (global).

```json
{
  "hooks": {
    "SessionStart": [
      { "type": "command", "command": "/path/to/your/script.sh" }
    ],
    "UserPromptSubmit": [
      { "type": "command", "command": "python3 /path/to/validate_prompt.py" }
    ],
    "PreToolUse": [
      { "type": "command", "command": "node /path/to/guard.js" }
    ]
  }
}
```

Multiple hooks per event run in array order. Put fast/critical hooks first.

## Example 1: Bash — Load Environment on SessionStart

A simple hook that tells the agent about the current environment:

```bash
#!/bin/bash
# hooks/session-env.sh — Load project context at session start

# Build context about the current environment
BRANCH=$(git branch --show-current 2>/dev/null || echo "unknown")
NODE_VER=$(node --version 2>/dev/null || echo "not installed")
LAST_COMMIT=$(git log --oneline -1 2>/dev/null || echo "no commits")

# Output JSON with systemMessage (for SessionStart events)
cat <<EOF
{
  "systemMessage": "Project environment: branch=${BRANCH}, node=${NODE_VER}, last commit: ${LAST_COMMIT}"
}
EOF
```

Make it executable: `chmod +x hooks/session-env.sh`

## Example 2: Python — Validate Prompts on UserPromptSubmit

A hook that warns when prompts are too vague:

```python
#!/usr/bin/env python3
"""hooks/validate_prompt.py — Check prompt quality."""
import json
import sys

def main():
    data = json.load(sys.stdin)
    prompt = data.get("prompt", "")

    # Skip short/vague prompts
    vague_patterns = ["fix it", "do the thing", "make it work", "help"]
    is_vague = any(prompt.strip().lower() == p for p in vague_patterns)

    if is_vague:
        output = {
            "hookSpecificOutput": {
                "hookEventName": "UserPromptSubmit",
                "additionalContext": (
                    "Note: The user's prompt is vague. "
                    "Ask a clarifying question before proceeding."
                )
            }
        }
    else:
        output = {}

    json.dump(output, sys.stdout)

if __name__ == "__main__":
    main()
```

## Example 3: Node — Log Tool Usage on PostToolUse

A hook that logs every tool call for later analysis:

```javascript
#!/usr/bin/env node
// hooks/log-tools.js — Log tool usage to a file
const fs = require('fs');

const input = JSON.parse(require('fs').readFileSync('/dev/stdin', 'utf8'));

const entry = {
  timestamp: new Date().toISOString(),
  tool: input.tool_name || 'unknown',
  session: input.sessionId || 'unknown'
};

// Append to log file
const logPath = '.claude/tool-usage.log';
fs.appendFileSync(logPath, JSON.stringify(entry) + '\n');

// Output empty JSON (no context to inject)
process.stdout.write('{}');
```

## Environment Variables Available to Hooks

Claude Code sets these environment variables before running your hook:

| Variable | Description |
|----------|-------------|
| `CLAUDE_SESSION_ID` | Current session identifier |
| `CLAUDE_PROJECT_DIR` | Path to the project root |
| `HOME` | User's home directory |
| `PATH` | Standard system PATH |

The hook's working directory is the project root.

## Testing Hooks Manually

Always test your hook outside of Claude Code first:

```bash
# Test a SessionStart hook (no stdin needed for basic ones)
echo '{"sessionId":"test-123","hookEventName":"SessionStart"}' | ./hooks/session-env.sh

# Test a UserPromptSubmit hook
echo '{"prompt":"fix the auth bug","sessionId":"test-123"}' | python3 hooks/validate_prompt.py

# Test a PreToolUse hook
echo '{"tool_name":"Bash","tool_input":{"command":"rm -rf /"}}' | node hooks/guard.js
echo $?  # Check exit code: 0 = allow, non-zero = block
```

If it works in the terminal, it will work as a hook. If it fails in the terminal, it will fail as a hook (and you will have a hard time figuring out why from inside Claude Code).

## Keeping Hooks Fast

Hooks run synchronously. The user waits for every hook to finish. Aim for under 100ms. Over 500ms is noticeable. Over 2 seconds is unacceptable for hooks that fire on every message (UserPromptSubmit, PreToolUse).

Tips: avoid network calls in per-message hooks, cache expensive computations to disk, use compiled languages (Go, Rust) for performance-critical hooks, and set your own timeouts with `timeout 2 your-command`.

## Common Mistakes

1. **Forgetting `chmod +x`** — Your script must be executable.
2. **Using relative paths** — Always use absolute paths in settings.json, or paths relative to the project root.
3. **Printing to stdout accidentally** — Debug output must go to stderr (`>&2`), not stdout. Stdout is parsed as JSON.
4. **Not outputting JSON** — Even if you have nothing to say, output `{}`.
5. **Blocking on user input** — Hooks cannot prompt the user. They run non-interactively.
6. **Heavy computation on UserPromptSubmit** — This fires on every single message. Keep it light.

## Project-Level vs Global Hooks

| Location | Scope | Use case |
|----------|-------|----------|
| `.claude/settings.json` | This project only | Project-specific guards, context loading |
| `~/.claude/settings.json` | All projects | Global safety rails, logging, analytics |

Project-level hooks run in addition to global hooks, not instead of them.
