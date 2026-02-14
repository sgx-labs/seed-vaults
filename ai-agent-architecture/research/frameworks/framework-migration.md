---
title: "How Do I Migrate Between Agent Frameworks?"
tags: [migration, frameworks, refactoring, portability, lock-in]
content_type: research
domain: engineering
---

# How Do I Migrate Between Agent Frameworks?

## The Question

How do you move an agent from one framework to another without rewriting everything?

## Why Migrations Happen

- Framework no longer maintained or falls behind
- Performance or cost issues with current framework
- Team adopts a different standard
- Outgrow framework's capabilities
- Need features only available in another framework

## What Is Portable

| Component | Portability | Notes |
|-----------|-------------|-------|
| Tool implementations | High | Business logic is framework-agnostic |
| MCP servers | High | Protocol-standard, works everywhere |
| System prompts | High | Just text — works with any model |
| Quality test datasets | High | Input/output pairs are framework-agnostic |
| Conversation logs | Medium | Format varies, but content transfers |
| State schemas | Medium | May need type conversion |
| Orchestration logic | Low | Framework-specific graphs, crews, etc. |
| Framework-specific hooks | Low | Tied to framework internals |

## Migration Strategy

### Step 1: Isolate Portable Components

Before migrating, refactor to separate portable from framework-specific code:

```python
# GOOD: Tool logic is framework-agnostic
def search_codebase(query: str, top_k: int = 10) -> list[dict]:
    """Pure business logic. No framework imports."""
    return vector_db.search(query, top_k=top_k)

# Framework-specific wrapper (easy to replace)
@langchain_tool  # or @crewai_tool, or MCP tool definition
def search_code_tool(query: str) -> str:
    results = search_codebase(query)
    return json.dumps(results)
```

### Step 2: Use MCP as the Portability Layer

If your tools are MCP servers, they work with any framework:

```python
# This MCP server works with Claude SDK, LangGraph, CrewAI, or custom
server = McpServer("my-tools")

@server.tool("search_code", "Search the codebase")
async def search_code(query: str):
    results = search_codebase(query)
    return {"content": [{"type": "text", "text": json.dumps(results)}]}
```

### Step 3: Migrate Orchestration

This is the framework-specific part. Map concepts between frameworks:

| From | To | Mapping |
|------|-----|---------|
| LangGraph node | CrewAI agent | Node function -> Agent role + task |
| LangGraph edge | CrewAI process | Conditional edge -> Process.sequential/hierarchical |
| LangGraph state | CrewAI context | TypedDict -> Task context parameter |
| CrewAI crew | LangGraph graph | Crew -> Graph with supervisor node |
| Custom loop | LangGraph | While loop -> Graph with conditional edges |
| Custom loop | Claude SDK | Already compatible (SDK is a loop) |

### Step 4: Verify with Quality Tests

Run your existing quality test suite against the new implementation:

```python
# Same test dataset, different framework
old_results = run_quality_suite(old_agent, dataset)
new_results = run_quality_suite(new_agent, dataset)

# Compare
for old, new in zip(old_results, new_results):
    if abs(old.pass_rate - new.pass_rate) > 0.05:
        print(f"Regression on {old.task}: {old.pass_rate} -> {new.pass_rate}")
```

## Reducing Future Migration Pain

1. **Keep tool logic framework-agnostic.** Pure functions with no framework imports.
2. **Use MCP for tool interfaces.** Standard protocol, works everywhere.
3. **Store prompts as separate files.** Not embedded in framework-specific code.
4. **Maintain quality test suites.** Framework-agnostic input/output assertions.
5. **Document orchestration decisions.** Why this pattern, not that one.

## See Also

- `hub/frameworks.md` — Framework comparison
- `entities/mcp-protocol.md` — MCP for portability
- `research/frameworks/build-vs-framework.md` — Build vs framework
