---
title: "Hook Lifecycle and Execution Model"
tags: [hooks, lifecycle, events, SessionStart, Stop, execution-model]
content_type: research
domain: engineering
---

# Hook Lifecycle and Execution Model

## The Question

What happens at each stage of a Claude Code session, which hooks fire in what order, and how does hook output reach the agent?

## Session Timeline

A complete session fires hooks in this order:

```
1. SessionStart       — session opens
2. UserPromptSubmit   — user types first message
3. PreToolUse         — agent decides to call a tool
4. [tool executes]
5. PostToolUse        — tool returns its result
6. (repeat 2-5 for each turn)
7. Stop               — session ends (best-effort)
```

Not every hook fires every session. If the agent never uses a tool, PreToolUse and PostToolUse never fire. If the session crashes, Stop may not fire at all.

## Hook-by-Hook Details

### SessionStart

**When:** Immediately when `claude` starts a new session.
**Guaranteed:** Yes. This is the only hook that always fires.
**Input (stdin):** JSON with session metadata.

```json
{
  "sessionId": "abc123",
  "hookEventName": "SessionStart"
}
```

**Output:** Whatever you print to stdout becomes a system message visible to the agent. Use this to load orientation context — project state, recent decisions, handoff notes from the last session.

**Best for:** Loading context, checking environment, registering the session.

### UserPromptSubmit

**When:** Every time the user sends a message (after they press Enter).
**Guaranteed:** Yes, for every user message.
**Input (stdin):** JSON with the user's prompt text.

```json
{
  "prompt": "How do I fix the auth middleware?",
  "sessionId": "abc123",
  "hookEventName": "UserPromptSubmit"
}
```

**Output:** JSON with `hookSpecificOutput` containing `additionalContext`. This context is injected alongside the user's message so the agent sees it before responding.

```json
{
  "hookSpecificOutput": {
    "hookEventName": "UserPromptSubmit",
    "additionalContext": "Relevant notes about auth middleware..."
  }
}
```

**Best for:** Surfacing relevant context, validating prompts, logging user activity.

### PreToolUse

**When:** After the agent decides to use a tool but before the tool executes.
**Guaranteed:** Yes, for every tool call.
**Input (stdin):** JSON with the tool name and parameters.

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

**Output:** Return JSON with `hookSpecificOutput`. To block the tool, exit with a non-zero code and include a reason.

**Best for:** Blocking dangerous operations, enforcing project rules, audit logging.

### PostToolUse

**When:** After a tool finishes executing.
**Guaranteed:** Yes, for every completed tool call.
**Input (stdin):** JSON with the tool name and its result.

**Best for:** Logging tool usage, triggering side effects, collecting metrics.

### Stop

**When:** When the session ends normally (user quits, conversation completes).
**Guaranteed:** No. Best-effort only.
**Input (stdin):** JSON with session ID and transcript path.

```json
{
  "sessionId": "abc123",
  "transcript_path": "/path/to/transcript.jsonl",
  "hookEventName": "Stop"
}
```

**Best for:** Saving handoff notes, cleaning up resources, session analytics. Never put critical logic here — the session might crash before Stop fires.

### Notification

**When:** The agent sends a notification (e.g., long task completed).
**Guaranteed:** Yes, when a notification is triggered.

**Best for:** Custom notification routing (Slack, email, desktop alerts).

### SubagentStop

**When:** A subagent (parallel worker) finishes its task.
**Guaranteed:** Yes, for each subagent completion.

**Best for:** Aggregating results from parallel subagents.

## How Hook Output Becomes Agent Context

The mechanism depends on the hook event:

| Hook Event | Output Field | How Agent Sees It |
|------------|-------------|-------------------|
| SessionStart | `systemMessage` | System-level context at session start |
| UserPromptSubmit | `hookSpecificOutput.additionalContext` | Injected with the user's message |
| PreToolUse | `hookSpecificOutput.additionalContext` | Seen before tool execution decision |
| PostToolUse | `hookSpecificOutput.additionalContext` | Seen after tool result |
| Stop | `systemMessage` | System message (if session is still alive) |

The key distinction: `hookSpecificOutput` is only valid for UserPromptSubmit, PreToolUse, and PostToolUse. SessionStart and Stop must use top-level `systemMessage`.

## Error Handling

- **Non-zero exit code:** The hook is marked as failed. The agent may see an error message, but the session continues.
- **Empty stdout:** Claude Code treats this as a hook failure. Always output at least `{}`.
- **Crash/panic:** The session continues without the hook's output. Design hooks to fail gracefully.
- **Multiple hooks per event:** They run in array order. One hook failing does not prevent subsequent hooks from running.

## Timeout Behavior

Hooks run synchronously — the session waits for them to finish. If a hook takes too long, it blocks the user. There is no official global timeout, but well-behaved hooks should complete in under 2 seconds.

For hooks that call external services (embedding providers, APIs), implement your own timeout logic:

```bash
#!/bin/bash
# Self-imposed 5-second timeout
timeout 5 curl -s https://api.example.com/context || echo '{}'
```

## Key Design Rules

1. **SessionStart is your one guaranteed shot.** Load everything important here.
2. **Stop is best-effort.** Never rely on it for critical data persistence.
3. **Keep hooks fast.** They run synchronously and block the user.
4. **Always output valid JSON.** Empty stdout = hook failure.
5. **Fail gracefully.** A broken hook should not break the session.
6. **Context counts against tokens.** Every byte of hook output consumes context window space. Be concise.
