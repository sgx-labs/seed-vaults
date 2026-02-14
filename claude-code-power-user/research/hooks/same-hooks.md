---
title: "How SAME Uses Hooks for Persistent Memory"
tags: [hooks, same, memory, context-surfacing, handoff, session-continuity, crash-recovery, pinned-notes]
content_type: research
domain: engineering
---

# How SAME Uses Hooks for Persistent Memory

## The Question

How does SAME turn the hooks system into a persistent memory layer for Claude Code? What does each hook do, and how do they work together?

## The Core Pattern

SAME registers 6 hooks across the Claude Code lifecycle. Together they create a cycle:

```
Hooks DEPOSIT context     →  Agent reads it passively
MCP tools SEARCH on demand  →  Agent queries when needed
```

The agent never has to ask "what did we work on last time?" or "are there any relevant notes?" — the hooks surface that information automatically. MCP tools handle explicit searches ("find notes about authentication").

## The 6 Hooks

### 1. session-start (SessionStart)

**Purpose:** Orient the agent at the start of every session.

**What it loads:**
- Previous session handoff (what happened last time, what is pending)
- Pinned notes (the user's most important context, always surfaced)
- Active decisions from the last 7 days
- Stale notes that need review
- Active parallel sessions (so the agent knows about other running instances)

**How it works:** Reads the vault database, collects each section within a token budget (~2000 tokens total), wraps it in a `<session-bootstrap>` tag, and outputs it as a `systemMessage`.

**Why it matters:** This is the only guaranteed hook. SAME treats it as the one chance to give the agent everything it needs to be productive immediately. The agent starts every session already knowing what happened, what is pending, and what the user cares about.

### 2. session-bootstrap (SessionStart)

**Purpose:** Recover context from crashed sessions.

**What it does:** Uses a priority cascade to find the previous session:
1. Look for a handoff note (the ideal case — previous session ended cleanly)
2. Look for a registered instance (previous session was running but may have crashed)
3. Fall back to a session index lookup

If the previous session crashed before its Stop hook fired, there is no handoff note. The bootstrap hook reconstructs what it can from the instance registry and session metadata.

**Why it matters:** Sessions crash. Browsers close. Laptops sleep. The "Survive the Crash" design principle means SAME never relies on graceful exits for critical context.

### 3. user-prompt / context-surfacing (UserPromptSubmit)

**Purpose:** Surface relevant vault notes for every user message.

**What it does:**
1. Reads the user's prompt from stdin
2. Skips short prompts (< 20 chars), slash commands, and conversational/social messages
3. Detects if this is a topic change or a continuation of the same discussion
4. Embeds the prompt using the local embedding provider (Ollama)
5. Searches the vault for semantically similar notes
6. Ranks results using a composite score (semantic similarity + title overlap + recency + content type priority)
7. Applies a token budget (~800 tokens, max 3 results)
8. Wraps the results in a `<vault-context>` tag

**Key behaviors:**
- **Topic change detection:** If the user is still talking about the same topic, the hook skips the search. Context is already in the conversation window.
- **Mode detection:** Classifies the prompt as exploring, deepening, executing, or socializing. Execution-mode prompts ("fix it", "run the test") skip the search — the agent already has context.
- **Low-signal gate:** If the prompt lacks specific terms (no domain words, no quoted phrases), the search would return noise. The hook skips it.

**Why it matters:** This is the hook that makes SAME feel like the agent "remembers." Without it, every session starts from scratch. With it, the agent sees relevant notes from across the knowledge base without the user having to search manually.

### 4. handoff-generator (Stop)

**Purpose:** Save a session handoff note when the session ends.

**What it does:**
1. Reads the transcript from the path provided in stdin
2. Extracts key sections: what was accomplished, what is pending, decisions made, files changed
3. Writes a timestamped handoff note to the sessions/ directory

**Why it matters:** The handoff is what the next session's bootstrap hook reads. It creates continuity between sessions. But because Stop is best-effort, SAME also has crash recovery as a fallback.

### 5. pre-tool-use (PreToolUse)

**Purpose:** Block dangerous operations before they execute.

**What it does:** Inspects the tool name and parameters from stdin. Currently used for push protection — preventing accidental pushes to protected branches or force pushes.

**Example:** When the agent tries to run `git push --force origin main`, the PreToolUse hook reads the Bash command, detects the force-push to main, and exits with a non-zero code to block it.

**Why it matters:** Hooks are the only place you can intercept and block tool calls. Once the tool executes, the damage is done.

### 6. notification (Notification)

**Purpose:** Custom notification routing.

**What it does:** Receives notification events and can route them to custom destinations (desktop notifications, Slack, etc.).

## How They Work Together — A Full Session

```
Session opens
  └→ SessionStart fires
      └→ session-bootstrap: loads handoff from last session
      └→ session-start: loads pinned notes + decisions + stale notes
      └→ Agent sees: "Last session worked on auth. Pending: fix the rate limiter."

User types: "Let's work on the rate limiter"
  └→ UserPromptSubmit fires
      └→ context-surfacing: detects topic change, embeds prompt,
         finds "Rate Limiter Design" note in vault
      └→ Agent sees: relevant vault notes alongside the user's message

Agent decides to edit a file
  └→ PreToolUse fires
      └→ pre-tool-use: checks if the file is protected. It's not. Allows.

Agent decides to push changes
  └→ PreToolUse fires
      └→ pre-tool-use: detects push to main. Blocks with warning.

User types: "Ok, push to the feature branch instead"
  └→ UserPromptSubmit fires
      └→ context-surfacing: same topic, skips search (context already loaded)

Session ends
  └→ Stop fires (best-effort)
      └→ handoff-generator: extracts transcript, writes handoff note
      └→ Next session will pick up where this one left off
```

## The Design Principles Behind SAME Hooks

1. **Deposit Before You Withdraw.** The hooks write context (handoffs, decisions, session state) so that future searches and future sessions have something to find.

2. **SessionStart is the Only Guaranteed Hook.** Everything critical goes in the bootstrap. Stop is a nice-to-have.

3. **Survive the Crash.** If Stop never fires, the next session still recovers via the instance registry and session index.

4. **Show It's Working.** Every hook prints feedback to stderr so the user sees what SAME is doing (pinned notes loaded, handoff saved, notes surfaced). Quiet mode is the opt-out.

5. **Context is Expensive.** Every token of hook output competes with the user's actual work for context window space. SAME enforces strict token budgets on every hook.

## Plugin System

SAME also supports custom plugins that run alongside the built-in hooks. Plugins are defined in `.same/plugins.json` in the vault:

```json
{
  "plugins": [
    {
      "name": "my-custom-plugin",
      "event": "UserPromptSubmit",
      "command": "/path/to/plugin",
      "args": ["--format", "json"],
      "timeout_ms": 5000,
      "enabled": true
    }
  ]
}
```

Plugin output is merged with the built-in hook output and wrapped in `<plugin-context>` tags. Plugin output is sanitized to prevent XML tag injection.
