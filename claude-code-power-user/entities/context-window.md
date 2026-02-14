---
title: "Context Window — Current State"
tags: [context-window, tokens, entity, performance, compaction]
content_type: entity
domain: engineering
---

# Context Window — Current State

## Token Limits

| Model | Context Window | Practical Limit |
|-------|---------------|----------------|
| Opus 4.6 | 200K tokens | ~150K before compaction |
| Sonnet 4.5 | 200K tokens | ~150K before compaction |
| Haiku 4.5 | 200K tokens | ~150K before compaction |

## How Compaction Works
When the context approaches the limit, Claude Code automatically compresses earlier messages. You'll see a system message about compaction. After compaction:
- Recent messages are preserved in full
- Older messages are summarized
- File contents read earlier may need to be re-read

## What Consumes Tokens
1. **System prompt** — CLAUDE.md, settings, MCP tool descriptions (~2-5K)
2. **File reads** — Each file read adds its content to context
3. **Tool results** — Command output, search results, etc.
4. **Conversation history** — Your messages + Claude's responses
5. **Hook output** — Whatever hooks print to stdout

## Management Strategies

| Strategy | How | When |
|----------|-----|------|
| Start fresh | New session | Context is clearly full |
| Use subagents | Task tool | Research that doesn't need main context |
| Reference by path | `src/auth.ts:23` | Instead of pasting code |
| Batch operations | One detailed prompt | Instead of many small ones |
| Plan mode | `/plan` or EnterPlanMode | Complex tasks that need exploration |
| Compact manually | `/compact` | When context feels bloated |

## Signs Your Context Is Full
- Responses slow down noticeably
- Claude forgets things from earlier in the conversation
- Compaction messages appear
- Claude re-reads files it already read

## Last Updated
February 2026
