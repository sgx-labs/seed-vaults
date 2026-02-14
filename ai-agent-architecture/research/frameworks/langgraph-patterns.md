---
title: "How Do I Build Agents with LangGraph?"
tags: [LangGraph, LangChain, graph, state-machine, patterns]
content_type: research
domain: engineering
---

# How Do I Build Agents with LangGraph?

## The Question

What are the practical patterns for building production agents with LangGraph?

## Core Concept: Agents as Graphs

LangGraph models agent workflows as directed graphs. Nodes are functions or LLM calls. Edges define transitions. State flows through the graph and is updated at each node.

```python
from langgraph.graph import StateGraph, END
from typing import TypedDict, Annotated
from operator import add

class AgentState(TypedDict):
    messages: Annotated[list, add]  # Messages accumulate
    next_action: str

def research_node(state: AgentState):
    # LLM call with research tools
    result = research_agent.invoke(state["messages"])
    return {"messages": [result], "next_action": "analyze"}

def analyze_node(state: AgentState):
    # LLM call with analysis tools
    result = analysis_agent.invoke(state["messages"])
    return {"messages": [result], "next_action": "done"}

def route(state: AgentState) -> str:
    if state["next_action"] == "done":
        return END
    return state["next_action"]

# Build the graph
graph = StateGraph(AgentState)
graph.add_node("research", research_node)
graph.add_node("analyze", analyze_node)
graph.set_entry_point("research")
graph.add_conditional_edges("research", route)
graph.add_conditional_edges("analyze", route)

app = graph.compile()
```

## Key Patterns

### Supervisor Pattern

One node routes tasks to specialist workers:

```python
def supervisor(state):
    decision = llm.invoke(
        "Given the current state, which agent should handle next? "
        "Options: researcher, coder, reviewer, FINISH"
    )
    return {"next_agent": decision.content}

graph.add_node("supervisor", supervisor)
graph.add_node("researcher", researcher_node)
graph.add_node("coder", coder_node)
graph.add_node("reviewer", reviewer_node)
graph.add_conditional_edges("supervisor", route_to_agent)
```

### Human-in-the-Loop

Interrupt execution for human approval:

```python
# Compile with interrupt points
app = graph.compile(
    checkpointer=SqliteSaver.from_conn_string("checkpoints.db"),
    interrupt_before=["execute_action"],
)

# Run until interrupt
result = app.invoke(input, config)
# -> Pauses at execute_action node

# Human reviews, then resumes
result = app.invoke(None, config)  # Continue from checkpoint
```

### Subgraph Composition

Build modular, reusable graph components:

```python
# Research subgraph
research_graph = StateGraph(ResearchState)
research_graph.add_node("search", search_node)
research_graph.add_node("summarize", summarize_node)
research_subgraph = research_graph.compile()

# Main graph uses research as a node
main_graph = StateGraph(MainState)
main_graph.add_node("research", research_subgraph)
main_graph.add_node("implement", implement_node)
```

### Time-Travel Debugging

Inspect and replay from any checkpoint:

```python
# List all checkpoints
for cp in checkpointer.list(config):
    print(f"Step {cp.metadata['step']}: {cp.channel_values['next_action']}")

# Resume from a specific checkpoint
app.invoke(None, config={
    "configurable": {
        "thread_id": "task-123",
        "checkpoint_id": "cp_abc",
    }
})
```

## When LangGraph Is the Right Choice

- Complex workflows with conditional branching
- You need checkpoints and resumability
- Multi-agent orchestration with custom routing
- Time-travel debugging is valuable
- Team is in the LangChain ecosystem

## Limitations

- Learning curve for graph-based thinking
- Adds abstraction that may hide simple patterns
- Python-centric (JS support exists but less mature)
- Can be over-engineered for simple agents

## See Also

- `entities/langgraph.md` — Current state and capabilities
- `hub/frameworks.md` — Framework comparison
- `research/architecture/state-management.md` — State management patterns
