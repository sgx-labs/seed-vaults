---
title: "Workflows — Daily Patterns for Claude Code"
tags: [workflows, efficiency, patterns, daily]
content_type: hub
domain: engineering
---

# Workflows — Daily Patterns for Claude Code

## Overview

Claude Code is most effective when you work with it in patterns. These aren't rigid rules — they're habits that save tokens and get better results.

## Core Workflow Patterns

### 1. Feature Development
Plan first, then execute. Don't jump straight to code for non-trivial features.

```
You: "Add user authentication with JWT"
Claude: [enters plan mode, explores codebase, presents approach]
You: [approve or adjust plan]
Claude: [implements, tests, commits]
```

See: `research/workflows/plan-mode.md`

### 2. Bug Fixing
Give context. Error messages, reproduction steps, and stack traces help Claude zero in fast.

```
You: "Fix this error: TypeError: Cannot read property 'id' of undefined
     It happens when I click 'Save' on an empty form"
```

See: `research/debugging/effective-bug-reports.md`

### 3. Code Review
Point Claude at specific changes, not "review everything."

```
You: "Review the changes in the last 3 commits for security issues"
You: "Review the auth middleware for common vulnerabilities"
```

See: `research/workflows/code-review-workflow.md`

### 4. Refactoring
Use plan mode. Refactoring touches many files and Claude needs to understand the full picture.

See: `research/workflows/refactoring-workflow.md`

### 5. Git Operations
Claude handles commits, PRs, and branch management. Be specific about what to include.

See: `research/workflows/git-workflow.md`

## Efficiency Patterns

| Pattern | Why | See |
|---------|-----|-----|
| Batch related requests | Fewer prompts = fewer tokens | `research/prompts/batching-operations.md` |
| Use plan mode for big tasks | Prevents wasted implementation | `research/workflows/plan-mode.md` |
| Reference files by path | More precise than descriptions | `research/prompts/effective-prompts.md` |
| Use slash commands | Faster than typing full instructions | `research/workflows/slash-commands.md` |
| Let Claude use subagents | Parallelizes independent work | `research/advanced/subagents.md` |

## Anti-Patterns (What Wastes Tokens)

1. **Rapid-fire single-line prompts** — Batch them instead
2. **Asking the same question differently** — Be specific the first time
3. **Not providing context** — Share error messages, not just "it's broken"
4. **Fighting the plan** — If Claude suggests plan mode, there's usually a reason
5. **Manually doing what Claude can automate** — Use hooks for repetitive tasks

## Research Notes

- `research/workflows/daily-workflow.md` — Structuring your day
- `research/workflows/plan-mode.md` — When and how to use plan mode
- `research/workflows/slash-commands.md` — Built-in slash commands
- `research/workflows/git-workflow.md` — Git operations
- `research/workflows/code-review-workflow.md` — Code review patterns
- `research/workflows/refactoring-workflow.md` — Refactoring safely
