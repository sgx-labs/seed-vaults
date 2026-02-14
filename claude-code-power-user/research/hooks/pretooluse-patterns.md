---
title: "PreToolUse Guard Patterns"
tags: [hooks, PreToolUse, security, guard, git-push, protection, force-push, config-files]
content_type: research
domain: engineering
---

# PreToolUse Guard Patterns

## The Question

How do I use PreToolUse hooks to block dangerous operations before they execute?

## How PreToolUse Works

PreToolUse fires after the agent decides to use a tool but before the tool executes. Your hook receives the tool name and parameters on stdin as JSON:

```json
{
  "tool_name": "Bash",
  "tool_input": {
    "command": "git push --force origin main"
  },
  "sessionId": "abc123",
  "hookEventName": "PreToolUse"
}
```

To **allow** the operation: exit 0, optionally output context.
To **block** the operation: exit non-zero, output a reason.

```json
{
  "hookSpecificOutput": {
    "hookEventName": "PreToolUse",
    "additionalContext": "BLOCKED: Force-push to main is not allowed."
  }
}
```

## Pattern 1: Block Force Push

The most common guard. Prevent `git push --force` to protected branches.

```bash
#!/bin/bash
# hooks/no-force-push.sh — Block force pushes to main/master

input=$(cat /dev/stdin)
tool=$(echo "$input" | python3 -c "import sys,json; print(json.load(sys.stdin).get('tool_name',''))")
command=$(echo "$input" | python3 -c "import sys,json; print(json.load(sys.stdin).get('tool_input',{}).get('command',''))")

# Only inspect Bash tool calls
if [ "$tool" != "Bash" ]; then
    echo '{}'
    exit 0
fi

# Check for force push to protected branches
if echo "$command" | grep -qE 'git\s+push\s+.*--force|git\s+push\s+-f'; then
    if echo "$command" | grep -qE 'main|master|production'; then
        cat <<EOF
{
  "hookSpecificOutput": {
    "hookEventName": "PreToolUse",
    "additionalContext": "BLOCKED: Force-push to a protected branch (main/master/production) is not allowed. Push to a feature branch instead."
  }
}
EOF
        exit 1
    fi
fi

echo '{}'
exit 0
```

## Pattern 2: Protect Production Config Files

Block writes to files that should not be modified by the agent.

```python
#!/usr/bin/env python3
"""hooks/protect_configs.py — Block writes to production configs."""
import json
import sys

PROTECTED_FILES = [
    ".env.production",
    "config/production.yml",
    "docker-compose.prod.yml",
    "terraform/prod/",
    ".github/workflows/deploy.yml",
]

def main():
    try:
        data = json.load(sys.stdin)
    except Exception:
        print('{}')
        sys.exit(0)

    tool = data.get("tool_name", "")
    tool_input = data.get("tool_input", {})

    # Check Write and Edit tools
    if tool in ("Write", "Edit"):
        file_path = tool_input.get("file_path", "")
        for protected in PROTECTED_FILES:
            if protected in file_path:
                output = {
                    "hookSpecificOutput": {
                        "hookEventName": "PreToolUse",
                        "additionalContext": (
                            f"BLOCKED: {file_path} is a production config file. "
                            "Modify it manually or create a non-production copy."
                        )
                    }
                }
                json.dump(output, sys.stdout)
                sys.exit(1)

    print('{}')
    sys.exit(0)

if __name__ == "__main__":
    main()
```

## Pattern 3: Block Commits to Main

Prevent the agent from committing directly to the main branch.

```bash
#!/bin/bash
# hooks/no-commit-to-main.sh — Require feature branches

input=$(cat /dev/stdin)
tool=$(echo "$input" | python3 -c "import sys,json; print(json.load(sys.stdin).get('tool_name',''))")
command=$(echo "$input" | python3 -c "import sys,json; print(json.load(sys.stdin).get('tool_input',{}).get('command',''))")

if [ "$tool" != "Bash" ]; then
    echo '{}'
    exit 0
fi

# Only check git commit commands
if echo "$command" | grep -qE 'git\s+commit'; then
    branch=$(git branch --show-current 2>/dev/null)
    if [ "$branch" = "main" ] || [ "$branch" = "master" ]; then
        cat <<EOF
{
  "hookSpecificOutput": {
    "hookEventName": "PreToolUse",
    "additionalContext": "BLOCKED: You are on the ${branch} branch. Create a feature branch first: git checkout -b feature/your-feature"
  }
}
EOF
        exit 1
    fi
fi

echo '{}'
exit 0
```

## Real-World Example: SAME Push Protection

SAME uses a PreToolUse hook for push protection. The pattern:

1. Read the tool name and parameters from stdin
2. If it is a Bash tool call, extract the command string
3. Check if the command matches a push to a protected branch
4. If so, exit non-zero with a clear explanation
5. Otherwise, exit 0

The key insight: SAME's push protection is configured per-project. Users define protected branches in their vault config, and the hook reads that config at runtime. This makes the guard flexible without hardcoding branch names.

## Design Rules for Guard Hooks

1. **Be fast.** PreToolUse fires on every tool call. Target under 50ms. Check the tool name first and return `{}` immediately for tools you do not inspect.

2. **Be focused.** One guard per concern. Separate "block force push" and "protect config files" hooks are easier to debug and disable than one mega-hook.

3. **Fail open, not closed.** If your guard encounters an internal error (cannot parse JSON, cannot read config), let the operation through: `input=$(cat /dev/stdin 2>/dev/null) || { echo '{}'; exit 0; }`

4. **Give a clear reason.** When you block, tell the agent what went wrong and what to do instead. The agent adjusts its approach based on your message.

5. **Combine guards via multiple hooks.** Register separate PreToolUse entries in the hooks array. They run in order; if any exits non-zero, the tool is blocked. Each guard is independent -- one failing internally does not prevent others from running.
