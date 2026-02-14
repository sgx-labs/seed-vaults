---
title: "How to Structure Your Day Working with Claude Code"
tags: [workflow, daily, productivity, sessions, handoff, morning-routine, compact]
content_type: research
domain: engineering
---

# How to Structure Your Day Working with Claude Code

## The Core Idea

Claude Code sessions are stateless. Each new session starts with zero memory unless you give it context. The developers who get the most out of Claude Code structure their day around this reality: explicit starts, focused work blocks, and clean handoffs.

## Morning: Session Start

### 1. Orient Claude to Your Project

Start every session by letting Claude read your project context. If you use SAME or a similar memory layer, this happens automatically via hooks. If not, do it manually:

```
You: "Read CLAUDE.md and get oriented. What did we work on last?"
```

If you left a handoff note from yesterday:

```
You: "Read sessions/handoff.md and pick up where we left off."
```

### 2. Check the State of Things

Before diving into new work, understand what's in flight:

```
You: "Run git status and show me what's changed since the last commit."
You: "Are there any failing tests?"
```

### 3. Set the Day's Agenda

Tell Claude what you plan to work on. This helps it make better decisions about file exploration and tool use:

```
You: "Today I need to: 1) finish the auth middleware, 2) fix the date parsing bug from issue #42, 3) review the PR from yesterday."
```

## Working Blocks: Feature Work

### Use Plan Mode for New Features

For anything that touches more than two files, start in plan mode:

```
You: "I need to add rate limiting to the API. Use plan mode."
```

Claude will explore your codebase, design an approach, and present it before writing code. This prevents wasted implementation effort.

### Stay Focused Per Block

Each working block should have one goal. Mixing tasks in a single long session leads to context pollution and worse results.

```
Good:  "Implement the rate limiting middleware" [full implementation]
Good:  "Now let's switch to the date parsing bug" [new focus]

Bad:   "Add rate limiting and also fix that date bug and review the PR"
```

### Use /compact Between Tasks

When switching between unrelated tasks in the same session, compact the context:

```
/compact
You: "Now let's work on the date parsing bug in utils/parse.go"
```

This summarizes the conversation so far and frees up context window space.

## Working Blocks: Bug Fixes

### Give Full Context Upfront

The best bug fix prompts include three things:

```
You: "Fix this error in the signup flow:
     Error: UNIQUE constraint failed: users.email
     Steps: 1. Go to /signup 2. Enter existing email 3. Click submit
     Expected: Show 'email already exists' error
     Actual: 500 internal server error
     File: server/handlers/auth.go line 142"
```

### Verify the Fix

Always ask Claude to verify:

```
You: "Run the tests for the auth package to confirm the fix."
```

## Working Blocks: Code Review

### Review PRs and Recent Commits

```
You: "Review the last 3 commits for bugs, security issues, and code quality."
You: "Review PR #15 — focus on error handling."
```

See `research/workflows/code-review-workflow.md` for detailed review patterns.

## End of Day: Handoff

### 1. Commit Your Work

Never leave uncommitted changes overnight:

```
You: "Show me the diff of all changes, then let's commit."
```

Review what Claude proposes. Never auto-commit without reading the diff.

### 2. Create a Handoff Note

Tell your future self (and future Claude sessions) what happened:

```
You: "Create a handoff note covering what we accomplished, what's still pending, and any blockers."
```

A good handoff includes:
- What was completed
- What's in progress (with file paths)
- Known issues or blockers
- Next steps

### 3. Push if Ready

```
You: "Push to the feature branch."
```

## Daily Rhythm Summary

| Time | Activity | Commands/Prompts |
|------|----------|-----------------|
| Start | Orient | Read handoff, `git status`, set agenda |
| Block 1 | Feature work | Plan mode, implement, test |
| Mid-day | Review | `/compact`, switch context |
| Block 2 | Bug fixes | Full context, verify fix |
| Block 3 | Review | Review PRs, commits |
| End | Wrap up | Commit, handoff note, push |

## Tips

- **One task per block.** Context switching within a session degrades quality.
- **Use `/compact` between blocks.** It keeps the context window clean.
- **Handoff notes are not optional.** Tomorrow-you will thank today-you.
- **Review every diff.** Claude is good but not infallible. You own the code.
- **Let Claude run tests.** Verifying work is fast and catches mistakes early.
- **Use `/cost` periodically.** Track your token spend to build intuition about what costs more.
