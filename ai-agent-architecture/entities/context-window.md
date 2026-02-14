---
title: "Context Window — Token Limits and Management"
tags: [context-window, tokens, limits, management, entity, token-budget, pruning, compaction]
content_type: entity
domain: engineering
---

# Context Window — Token Limits and Management

## What It Is

The context window is the total number of tokens (input + output) a model can process in a single request. It is the fundamental constraint on what an agent can know and do in any given turn. Every system prompt, conversation message, tool result, and generated response competes for the same token budget.

## Current Token Limits (February 2026)

| Model | Standard | Extended (Beta) | Output limit |
|-------|----------|----------------|-------------|
| Claude Opus 4.6 | 200K | 1M | 32K |
| Claude Sonnet 4.5 | 200K | 1M | 16K |
| Claude Haiku 4.5 | 200K | — | 8K |
| GPT-4.1 | 1M | — | 32K |
| GPT-5.2 | 400K | — | 32K |
| Gemini 2.5 Pro | 1M | — | 65K |
| Gemini 3 Pro | 10M | — | 65K |

## The Real Limits

Advertised context windows are theoretical maximums. In practice:

- **Performance degrades** before hitting the limit. Most models show measurable quality drops past 50-60% of their window.
- **Cost scales linearly** with input tokens. Filling a 1M window is expensive.
- **Latency increases** with context size. More tokens = slower responses.
- **Needle-in-a-haystack** tasks get harder as context grows.

## Management Strategies for Agents

### 1. Context Budget Allocation

```
System prompt:     ~2K tokens (fixed)
Tool definitions:  ~1K per tool (fixed)
Conversation:      Variable (grows each turn)
Tool results:      Variable (can be large)
Agent response:    Up to output limit
─────────────────────────────────────
Available context: Total - fixed overhead
```

### 2. Pruning Techniques

- **Summarize old turns** — Replace early conversation with a summary
- **Truncate tool results** — Only include relevant portions of large results
- **Drop irrelevant messages** — Remove messages that do not affect the current task
- **Compaction** — Let the model summarize its own context, then continue

### 3. Just-in-Time Retrieval

Instead of front-loading context, retrieve what the agent needs when it needs it:
- Use RAG to pull relevant knowledge at query time
- Use MCP tools to fetch data on demand
- Keep the base context small, expand only when needed

### 4. Multi-Turn Budget Awareness

For long-running agents, track token usage across turns. When approaching limits:
- Trigger automatic compaction
- Summarize and continue in a new context window
- Hand off to a fresh agent with a structured summary

## The Token Tax

Every piece of infrastructure costs tokens:
- Each tool definition: 200-500 tokens
- System prompt: 500-3000 tokens
- MCP tool metadata: 100-300 tokens per tool
- Conversation overhead: ~50 tokens per message

With 20 tools and a detailed system prompt, you might spend 10K+ tokens before the agent does any work. Budget accordingly.

## Last Updated

February 2026
