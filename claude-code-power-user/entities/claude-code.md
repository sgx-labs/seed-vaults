---
title: "Claude Code — Current State"
tags: [claude-code, entity, models, capabilities]
content_type: entity
domain: engineering
---

# Claude Code — Current State

## What It Is
CLI-based AI coding agent from Anthropic. Runs in terminal, reads/writes files, executes commands, manages git.

## Models Available

| Model | ID | Best for | Speed | Cost |
|-------|-----|---------|-------|------|
| Opus 4.6 | claude-opus-4-6 | Complex architecture, multi-file changes | Slower | Highest |
| Sonnet 4.5 | claude-sonnet-4-5-20250929 | Daily coding, balanced quality/speed | Medium | Medium |
| Haiku 4.5 | claude-haiku-4-5-20251001 | Quick tasks, subagents, research | Fastest | Lowest |

## Key Capabilities
- Reads entire codebase for context
- Edits multiple files in a single turn
- Runs shell commands (with permission)
- Git operations (commit, PR, branch)
- Plan mode for complex tasks
- Subagents for parallel work
- Hook system for lifecycle automation
- MCP for external tool integration
- IDE integration (VS Code, Cursor, Windsurf)

## Tools Available
Read, Write, Edit, Glob, Grep, Bash, WebFetch, WebSearch, Task (subagents), NotebookEdit, AskUserQuestion, EnterPlanMode

## Slash Commands
`/help`, `/clear`, `/compact`, `/cost`, `/doctor`, `/init`, `/login`, `/logout`, `/memory`, `/model`, `/permissions`, `/review`, `/status`, `/vim`

## Context Window
200K tokens. Compaction happens automatically when approaching limits. See `entities/context-window.md`.

## Last Updated
February 2026
