---
title: "When Should I Build My Own Agent Framework?"
tags: [frameworks, build-vs-buy, custom, architecture, decision]
content_type: research
domain: engineering
---

# When Should I Build My Own Agent Framework?

## The Question

When is it worth building a custom agent framework instead of using an existing one?

## The Tradeoff

| Factor | Framework | Custom |
|--------|-----------|--------|
| Time to prototype | Hours | Days/weeks |
| Control | Framework's opinions | Total control |
| Debugging | Framework tools (LangSmith, etc.) | Your own tooling |
| Updates | Framework team maintains | You maintain |
| Lock-in | Framework-specific patterns | Your own abstractions |
| Community | Docs, examples, support | You figure it out |

## Build Your Own When

### 1. You Need Tight System Integration

If your agent needs to integrate deeply with existing infrastructure (custom auth, internal APIs, specific database patterns), a framework may add friction:

```python
# Custom agent loop that integrates with your existing systems
class MyAgent:
    def __init__(self, auth_service, data_layer, monitoring):
        self.auth = auth_service
        self.data = data_layer
        self.monitor = monitoring

    async def run(self, task):
        with self.monitor.trace("agent_task"):
            context = await self.data.get_context(task)
            result = await self.llm_loop(task, context)
            await self.monitor.record_completion(result)
            return result
```

### 2. Framework Abstractions Hide What You Need

If you find yourself fighting the framework — working around its patterns, monkey-patching internals, or spending more time understanding the framework than building your agent — it is adding cost, not value.

### 3. Your Use Case Is Simple

A single agent with tools is just a while loop:

```python
def run_agent(task, tools, model="claude-sonnet-4-5-20250929"):
    messages = [{"role": "user", "content": task}]
    for _ in range(MAX_TURNS):
        response = llm.create(model=model, tools=tools, messages=messages)
        if response.stop_reason == "end_turn":
            return response
        messages = handle_tool_calls(messages, response)
```

This is 10 lines. You do not need a framework for 10 lines.

### 4. You Are Building a Product

If your agent IS the product (not just a feature), you probably want full control over every aspect of its behavior.

## Use a Framework When

### 1. You Need State Management and Checkpoints

Building your own checkpoint system, time-travel debugging, and state persistence is significant work. LangGraph gives you this out of the box.

### 2. You Want Multi-Agent Orchestration

Coordinating multiple agents with routing, delegation, and result aggregation is complex. CrewAI and LangGraph have battle-tested patterns.

### 3. You Need Managed Deployment

LangGraph Platform provides deployment, API endpoints, and hosting. Building this infrastructure yourself takes months.

### 4. Your Team Benefits from Conventions

Frameworks enforce patterns. For teams, this consistency is valuable — everyone builds agents the same way, code is readable across the team.

## The Hybrid Approach

Use framework components without full framework lock-in:

```python
# Use LangChain's tool interface without the full framework
from langchain_core.tools import tool

@tool
def search_code(query: str) -> str:
    """Search the codebase for a pattern."""
    return search_engine.search(query)

# But use your own agent loop
response = my_custom_agent_loop(tools=[search_code])
```

## Decision Checklist

```
[ ] Is my use case simple (single agent, basic tools)? -> Consider custom
[ ] Do I need checkpoints and state persistence? -> Consider framework
[ ] Am I building a product or a feature? -> Product: custom. Feature: framework.
[ ] Does my team need conventions? -> Framework helps
[ ] Am I fighting the framework's opinions? -> Go custom
[ ] Do I need it working this week? -> Framework is faster
```

## See Also

- `hub/frameworks.md` — Framework comparison
- `research/frameworks/claude-sdk-patterns.md` — Claude SDK patterns
- `research/frameworks/langgraph-patterns.md` — LangGraph patterns
