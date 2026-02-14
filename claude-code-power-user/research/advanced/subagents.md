---
title: "Using Subagents for Parallel Work"
tags: [subagents, task-tool, parallel, context-management, advanced]
content_type: research
domain: engineering
---

# Using Subagents for Parallel Work

## The Question

How do you run multiple research or exploration tasks in parallel without bloating your main context window?

## What Subagents Are

A subagent is an independent worker agent that Claude Code spawns to handle a discrete task. Each subagent gets its own context window, runs independently, and returns only its result to the main conversation. The main agent's context stays clean.

Think of it like delegating to a colleague: you give them a clear task, they go do the research, and they come back with a summary. You never see the 47 files they read along the way.

## How to Launch a Subagent

Subagents are launched via the **Task** tool. You do not call this tool directly -- Claude decides to use it when the work benefits from isolation. But you can prompt Claude to use subagents explicitly:

```
You: "Use subagents to research how error handling works across
      the three main packages: api, store, and indexer."
```

Claude will launch separate Task calls, each scoped to one package.

### The Task Tool Parameters

| Parameter | Purpose |
|-----------|---------|
| `description` | What the subagent should do -- the clearer, the better |
| `subagent_type` | The kind of worker to spawn (see types below) |
| `run_in_background` | If `true`, the subagent runs asynchronously |

## Subagent Types

Different subagent types are optimized for different work:

| Type | Best For |
|------|----------|
| **Explore** | Codebase research, reading files, understanding architecture |
| **Bash** | Running commands, tests, builds, scripts |
| **Plan** | Designing approaches, analyzing trade-offs |
| General-purpose | Default; handles a mix of reading, analysis, and writing |

The `subagent_type` parameter controls which type is used. If you do not specify, the general-purpose agent is used.

### Example: Explore Subagents

```
You: "I need to understand how three subsystems work before refactoring.
      Use explore subagents to research:
      1. The authentication flow in src/auth/
      2. The database connection pooling in src/db/
      3. The caching layer in src/cache/
      Summarize each one separately."
```

Claude launches three Explore subagents in parallel. Each reads through its target directory, follows imports, and returns a focused summary. Your main context receives three compact summaries instead of the contents of 30+ files.

## Background Mode

For long-running tasks, set `run_in_background=true`. The subagent runs asynchronously while you continue working in the main conversation.

```
You: "Run the full test suite in the background and let me know
      when it's done."
```

Claude launches a Bash subagent in background mode. You keep working. When the tests finish, you get notified with the results.

### When to Use Background Mode

- Test suites that take more than a few seconds
- Build processes
- Any task where you do not need the result immediately
- Long codebase explorations while you work on something else

## Why Subagents Protect Your Context

Every file Claude reads, every grep result, every command output -- all of it consumes tokens from your context window. A single research task can easily burn 20-30K tokens on exploration that produces a 500-token answer.

Subagents solve this by isolating the exploration:

```
Without subagents:
  Main context: read 15 files + grep results + analysis = 25K tokens consumed
  Useful output: 500 tokens

With subagents:
  Subagent context: read 15 files + grep results + analysis = 25K tokens (discarded)
  Main context: 500-token summary returned
```

This matters most in long sessions where context is precious.

## Practical Patterns

### Pattern 1: Parallel Codebase Research

Before a refactoring task, research all affected areas simultaneously:

```
You: "Before we refactor the notification system, use subagents to
      research these three areas in parallel:
      1. All places that send notifications (grep for sendNotification)
      2. The notification template system in src/templates/
      3. The delivery queue in src/queue/
      Give me a summary of each."
```

### Pattern 2: Test and Implement Simultaneously

```
You: "Run the existing tests in the background while I describe
      the changes I want to make."
```

You describe the feature while tests run. When the subagent reports back, you both know the baseline test state.

### Pattern 3: Multi-Package Impact Analysis

```
You: "I'm going to change the User interface in shared/types.ts.
      Use subagents to check how this type is used in:
      1. The API package
      2. The worker package
      3. The frontend package
      Report which files and functions reference User in each."
```

### Pattern 4: Exploring Unfamiliar Code

When you are dropped into a codebase you do not know:

```
You: "I just cloned this repo. Use subagents to explore and summarize:
      1. The overall architecture (entry points, main packages)
      2. The test setup and coverage
      3. The build and deployment configuration
      Give me a 10-line summary of each."
```

## Tips

1. **Be specific in the task description.** Vague tasks produce vague results. "Research the auth system" is worse than "Find every middleware that checks JWT tokens and list the file paths."

2. **Scope each subagent to one concern.** Three focused subagents beat one broad one.

3. **Use background mode for anything slow.** Tests, builds, and large searches are good candidates.

4. **Ask for structured output.** "Return a bullet list of files and their roles" gives you more useful results than "tell me about it."

5. **Combine with plan mode.** Launch subagents to research, then use plan mode to design the approach based on their findings.

## Limitations

- Subagents cannot edit files in the main conversation's working state -- they are read-only researchers by default.
- Each subagent has its own context limit. Very large research tasks may need to be split further.
- Background subagents cannot ask you clarifying questions. The task description must be self-contained.
- Subagent results are summaries. If you need the raw data (exact file contents, full command output), you may need to re-read specific files in the main context.
