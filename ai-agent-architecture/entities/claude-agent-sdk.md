---
title: "Claude Agent SDK — Current State"
tags: [claude, agent-sdk, anthropic, entity, framework, tool-use, extended-thinking]
content_type: entity
domain: engineering
pinned: true
---

# Claude Agent SDK — Current State

## What It Is

Anthropic's official SDK for building AI agents with Claude. Available in Python (v0.1.x) and TypeScript (v0.2.x). Over 1.85M weekly downloads as of February 2026. Powers Claude Code, Anthropic's CLI coding agent.

## Core Architecture

The SDK implements a simple agent loop: the model receives context, decides to use a tool or respond, observes the result, and repeats until the task is complete. No graph, no state machine — just a capable model with tools in a loop.

```python
import anthropic

client = anthropic.Anthropic()

# Define tools the agent can use
tools = [
    {
        "name": "search_codebase",
        "description": "Search for files or code patterns in the project",
        "input_schema": {
            "type": "object",
            "properties": {
                "query": {"type": "string", "description": "Search query"}
            },
            "required": ["query"]
        }
    }
]

# Agent loop
messages = [{"role": "user", "content": "Find all TODO comments"}]
while True:
    response = client.messages.create(
        model="claude-sonnet-4-5-20250929",
        max_tokens=4096,
        tools=tools,
        messages=messages,
    )
    if response.stop_reason == "end_turn":
        break
    # Handle tool calls, append results, continue loop
```

## Key Capabilities

- **Tool use** — Define tools via JSON Schema, model calls them as needed
- **Extended thinking** — Model can reason before responding (budget_tokens parameter)
- **Streaming** — Token-by-token output for real-time UX
- **Multi-turn** — Conversation history management across turns
- **MCP integration** — Native support for MCP tool servers
- **Computer use** — Beta capability for GUI interaction
- **Prompt caching** — Reduce costs on repeated system prompts

## Models Available

| Model | Context | Best for |
|-------|---------|----------|
| Claude Opus 4.6 | 200K (1M beta) | Complex reasoning, architecture decisions |
| Claude Sonnet 4.5 | 200K (1M beta) | Balanced daily use, coding |
| Claude Haiku 4.5 | 200K | Fast tasks, classification, subagents |

## When to Use

- You are building a Claude-native agent
- Single-agent architecture is sufficient
- You want minimal abstraction over the model
- Coding agents or tool-heavy workflows

## When NOT to Use

- You need complex multi-agent state machines (use LangGraph)
- You need role-based agent teams (use CrewAI)
- You want model-agnostic code (use LangGraph or roll your own)

## Last Updated

February 2026
