---
title: "How Do I Build Agents with Claude Agent SDK?"
tags: [Claude, SDK, Anthropic, patterns, implementation]
content_type: research
domain: engineering
---

# How Do I Build Agents with Claude Agent SDK?

## The Question

What are the practical patterns for building production agents with Anthropic's Claude Agent SDK?

## Core Architecture

The Claude Agent SDK is deliberately minimal. It provides a tool-use loop and gets out of the way. The agent is the model — you provide tools, the model decides what to do.

```python
import anthropic

client = anthropic.Anthropic()

def run_agent(task: str, tools: list, max_turns: int = 20):
    messages = [{"role": "user", "content": task}]

    for turn in range(max_turns):
        response = client.messages.create(
            model="claude-sonnet-4-5-20250929",
            max_tokens=4096,
            system=SYSTEM_PROMPT,
            tools=tools,
            messages=messages,
        )

        # Append assistant response
        messages.append({"role": "assistant", "content": response.content})

        # Check if done
        if response.stop_reason == "end_turn":
            return extract_text(response)

        # Handle tool calls
        tool_results = []
        for block in response.content:
            if block.type == "tool_use":
                result = execute_tool(block.name, block.input)
                tool_results.append({
                    "type": "tool_result",
                    "tool_use_id": block.id,
                    "content": str(result),
                })

        messages.append({"role": "user", "content": tool_results})

    return "Max turns reached"
```

## Key Patterns

### Extended Thinking

For complex reasoning tasks, enable extended thinking:

```python
response = client.messages.create(
    model="claude-opus-4-6",
    max_tokens=16384,
    thinking={
        "type": "enabled",
        "budget_tokens": 10000,  # Up to 10K tokens of reasoning
    },
    messages=messages,
)
```

The model reasons internally before responding. Improves accuracy on multi-step problems.

### Prompt Caching

Cache system prompts and tool definitions to reduce costs:

```python
response = client.messages.create(
    model="claude-sonnet-4-5-20250929",
    max_tokens=4096,
    system=[{
        "type": "text",
        "text": LONG_SYSTEM_PROMPT,
        "cache_control": {"type": "ephemeral"},
    }],
    messages=messages,
)
```

### Subagents (via Claude Code)

Claude Code supports subagents via the Task tool — parallel workers that handle subtasks independently:

```
Main Agent: "I need to review 3 files for security issues"
  -> Subagent 1: Reviews auth.py
  -> Subagent 2: Reviews database.py
  -> Subagent 3: Reviews api.py
Main Agent: Aggregates results from all 3 subagents
```

Each subagent gets its own context window, preventing cross-contamination.

### MCP Integration

Connect to MCP servers for external tools:

```python
from anthropic import Anthropic
from anthropic.tools.mcp import MCPServerStdio

client = Anthropic()
server = MCPServerStdio(command="npx", args=["my-mcp-server"])

# Tools from the MCP server are available alongside native tools
```

## When Claude SDK Is the Right Choice

- Single-agent architectures (most common case)
- Claude-native development (best integration)
- Coding agents (Claude excels at code)
- Minimal abstraction preferred (SDK stays out of the way)
- You want extended thinking for complex reasoning

## Limitations

- No built-in multi-agent orchestration (use subagents or build your own)
- No built-in state persistence (manage conversation history yourself)
- Claude-only (not model-agnostic)
- No graph-based workflow definition

## See Also

- `entities/claude-agent-sdk.md` — Current state and capabilities
- `hub/frameworks.md` — Framework comparison
- `research/frameworks/build-vs-framework.md` — Build vs framework decision
