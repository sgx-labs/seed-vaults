---
title: "Debugging Hooks When They Don't Work"
tags: [hooks, debugging, troubleshooting, errors, testing]
content_type: research
domain: engineering
---

# Debugging Hooks When They Don't Work

## The Question

My hook is not working. How do I figure out what went wrong?

## The Debugging Checklist

Work through these in order. Most hook problems fall into the first three categories.

### 1. Is the hook registered in settings.json?

Claude Code only runs hooks defined in `.claude/settings.json` (project-level) or `~/.claude/settings.json` (global). Check both files.

```json
{
  "hooks": {
    "UserPromptSubmit": [
      {
        "type": "command",
        "command": "/absolute/path/to/your/hook.sh"
      }
    ]
  }
}
```

Common mistakes:
- Wrong top-level key (it is `hooks`, not `hook` or `lifecycle`)
- Wrong event name (`UserPromptSubmit`, not `user-prompt-submit` or `userPromptSubmit`)
- Missing the `type: "command"` field
- Using an array when you should not, or vice versa

The exact event names are case-sensitive:
```
SessionStart
Stop
UserPromptSubmit
PreToolUse
PostToolUse
Notification
SubagentStop
```

### 2. Can the command be found?

The most common hook failure: the command is not on the PATH or the path is wrong.

Test it directly in your terminal:

```bash
# If your hook is a script with an absolute path:
/path/to/your/hook.sh
# Does it run? If "command not found", the path is wrong.

# If your hook uses a command name (resolved via PATH):
which your-command
# Does it exist? If not, Claude Code cannot find it either.
```

Claude Code inherits the shell environment, but it may not source your `.bashrc` or `.zshrc` the same way your interactive terminal does. If your hook depends on a command installed via homebrew, nvm, pyenv, or similar, use the full absolute path.

```json
{
  "hooks": {
    "SessionStart": [
      {
        "type": "command",
        "command": "/opt/homebrew/bin/same hook session-start"
      }
    ]
  }
}
```

### 3. Does the hook work when you run it manually?

This is the single most effective debugging step. Run your hook exactly as Claude Code would:

```bash
# SessionStart — no meaningful stdin
echo '{}' | /path/to/your/hook.sh

# UserPromptSubmit — provide a test prompt
echo '{"prompt":"test prompt","sessionId":"debug-123"}' | /path/to/your/hook.sh

# PreToolUse — provide a test tool call
echo '{"tool_name":"Bash","tool_input":{"command":"ls"}}' | /path/to/your/hook.sh
```

Check:
- Does it produce valid JSON on stdout?
- Does it exit with code 0?
- Does it complete in under 2 seconds?

```bash
# Time it
time echo '{}' | /path/to/your/hook.sh

# Check exit code
echo '{}' | /path/to/your/hook.sh
echo "Exit code: $?"

# Validate the JSON output
echo '{}' | /path/to/your/hook.sh | python3 -m json.tool
```

### 4. Is the hook timing out or outputting too much?

Hooks run synchronously. If yours takes more than a few seconds (slow APIs, cold-starting Ollama, large file reads), it delays the session. Add your own timeout:

```bash
result=$(timeout 3 your-expensive-command 2>/dev/null) || { echo '{}'; exit 0; }
echo "$result"
```

Hook output also goes into the context window. If your hook prints thousands of lines, it wastes the agent's token budget. Cap output to under 4000 characters (~1000 tokens).

### 5. Is stdout vs stderr confused?

- **stdout** = parsed as JSON, injected into agent context
- **stderr** = logged for debugging, NOT seen by agent

Debug messages on stdout corrupt the JSON and cause a parse failure. Always send debug output to stderr with `>&2`.

### 6. Is the JSON output format correct?

Different hook events expect different JSON structures.

**SessionStart and Stop** use `systemMessage`:
```json
{
  "systemMessage": "Your context here"
}
```

**UserPromptSubmit, PreToolUse, PostToolUse** use `hookSpecificOutput`:
```json
{
  "hookSpecificOutput": {
    "hookEventName": "UserPromptSubmit",
    "additionalContext": "Your context here"
  }
}
```

Using `hookSpecificOutput` in a SessionStart hook will silently fail — the agent will not see your context.

## Finding Hook Errors in Claude Code

Claude Code does not prominently display hook errors. Here is where to look:

1. **System-reminder tags in conversation:** If a hook outputs context, it appears in `<system-reminder>` tags in the conversation. If you do not see these tags, the hook either did not run or produced no output.

2. **stderr output:** Hook stderr goes to the Claude Code process output. If you launched Claude Code from a terminal, check that terminal for error messages.

3. **Exit codes:** A non-zero exit code from a PreToolUse hook blocks the tool. A non-zero exit from other hooks is logged but does not block the session.

## Making Hooks Fail Gracefully

The golden rule: a broken hook should never break the session. Always exit 0 and output valid JSON, even when things go wrong internally.

```bash
#!/bin/bash
# Pattern: try the real work, fall back to empty output

run_hook() {
    # Your actual hook logic here
    result=$(some-command 2>/dev/null) || return 1
    echo "$result"
}

output=$(run_hook 2>/dev/null)
if [ $? -ne 0 ] || [ -z "$output" ]; then
    echo '{}'
else
    echo "$output"
fi
exit 0  # Always exit clean
```

The same pattern works in any language: wrap in try/except, output `{}` on failure, exit 0. The exception is PreToolUse hooks that intentionally block — those exit non-zero. But even PreToolUse hooks should exit 0 on internal errors, so a broken guard does not block all tool usage.

## Debugging Workflow Summary

```
1. Check settings.json — is the hook registered with the right event name?
2. Check the command path — does `which your-command` work?
3. Run it manually — pipe test JSON and check stdout + exit code
4. Check timing — does it finish in under 2 seconds?
5. Check output size — is it under 4000 characters?
6. Check stdout vs stderr — are debug messages going to stderr?
7. Check JSON format — systemMessage vs hookSpecificOutput for the event type?
8. Wrap in try/catch — make it fail gracefully
```
