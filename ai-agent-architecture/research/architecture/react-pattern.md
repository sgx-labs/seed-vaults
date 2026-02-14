---
title: "How Does the ReAct Pattern Work?"
tags: [ReAct, reasoning, action, agent-loop, architecture]
content_type: research
domain: engineering
---

# How Does the ReAct Pattern Work?

## The Question

What is ReAct, and why is it the dominant agent pattern?

## What ReAct Is

ReAct (Reason + Act) is an agent loop that alternates between thinking and doing. The agent reasons about what to do, takes an action (usually a tool call), observes the result, and uses that observation to inform the next thought.

```
Thought: I need to find the user's config file
Action:  search_files(pattern="config.*")
Observe: Found config.toml at /project/config.toml
Thought: Now I need to read it to check the database settings
Action:  read_file(path="/project/config.toml")
Observe: [file contents]
Thought: The database URL is set to localhost. I should update it.
Action:  edit_file(path="/project/config.toml", ...)
```

## Why It Works

1. **Grounded reasoning.** The model reasons about real data, not assumptions. Each observation provides facts.
2. **Reduced hallucination.** Instead of guessing, the agent looks things up.
3. **Natural tool use.** The think-act-observe cycle maps naturally to how tool-augmented LLMs work.
4. **Flexible.** No predefined plan — the agent adapts based on what it discovers.

## Implementation

Most frameworks implement ReAct as the default agent loop:

```python
# Simplified ReAct loop (framework-agnostic)
messages = [{"role": "user", "content": task}]

while True:
    response = llm.generate(messages, tools=available_tools)

    if response.has_tool_call:
        result = execute_tool(response.tool_call)
        messages.append({"role": "assistant", "content": response})
        messages.append({"role": "tool", "content": result})
    else:
        # Agent is done — final response
        return response.text
```

The Claude Agent SDK, OpenAI Agents SDK, and LangGraph all use this loop at their core. The difference is what they build around it (state management, routing, checkpoints).

## When ReAct Falls Short

| Limitation | Symptom | Better pattern |
|-----------|---------|----------------|
| No planning | Agent takes inefficient paths | Plan-and-Execute |
| No self-correction | Repeats same mistakes | Reflexion |
| Single reasoning path | Misses better approaches | Tree of Thoughts |
| Long tasks | Context window fills up | Multi-agent with handoffs |

## ReAct Best Practices

1. **Good tool descriptions.** The agent's "Thought" step depends on understanding what tools can do.
2. **Concise observations.** Truncate large tool results. The agent does not need the entire file.
3. **Stop conditions.** Set a maximum number of iterations to prevent infinite loops.
4. **Structured tool output.** Make observations easy to parse so the next thought is well-informed.

## See Also

- `hub/architecture.md` — Architecture overview
- `research/architecture/plan-and-execute.md` — When planning first is better
- `research/architecture/reflexion-pattern.md` — Adding self-correction to ReAct
