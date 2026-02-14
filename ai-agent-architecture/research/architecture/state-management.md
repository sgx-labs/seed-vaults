---
title: "What Are the Patterns for Agent State Management?"
tags: [state, management, persistence, checkpoints, architecture, scratchpad, working-memory, cross-session]
content_type: research
domain: engineering
---

# What Are the Patterns for Agent State Management?

## The Question

How do you manage and persist agent state across turns, sessions, and agent boundaries?

## Types of Agent State

| State type | Scope | Example | Persistence |
|-----------|-------|---------|-------------|
| Turn state | Single LLM call | Current tool call parameters | None needed |
| Conversation state | Within session | Message history | In-memory |
| Task state | Across turns | Progress on multi-step task | In-memory or DB |
| Session state | Full session | What was accomplished, what is pending | Filesystem/DB |
| Cross-session state | Between sessions | Decisions, preferences, knowledge | Persistent storage |

## State Management Patterns

### 1. Conversation History (Simplest)

The conversation itself is the state. The model reads prior messages to understand what happened.

```python
messages = [
    {"role": "user", "content": "Create a REST API for users"},
    {"role": "assistant", "content": "I'll start by creating the model..."},
    {"role": "user", "content": "Add pagination to the list endpoint"},
    # The model knows about the API from prior messages
]
```

**Limitation:** Context window fills up. Old messages get dropped or summarized.

### 2. Explicit State Object (LangGraph Pattern)

Define a typed state object that flows through the agent graph.

```python
class TaskState(TypedDict):
    goal: str
    steps_completed: list[str]
    current_step: str
    artifacts: dict  # files created, test results, etc.
    errors: list[str]
```

**Benefit:** State is inspectable, serializable, and independent of conversation history. You can checkpoint and resume from any point.

### 3. Scratchpad / Working Memory

The agent maintains a structured note that it updates as it works.

```markdown
## Working Memory
- Goal: Migrate database from PostgreSQL to MySQL
- Completed: Schema analysis, dependency mapping
- Current: Writing migration scripts
- Blocked: Need to handle JSONB columns (no MySQL equivalent)
- Decisions: Use JSON column type with application-level validation
```

**Benefit:** Human-readable state. The agent can be asked "what's your current status?" and answer from the scratchpad.

### 4. Checkpoint-Based (LangGraph)

Automatically persist state at each graph node. Resume from any checkpoint.

```python
# LangGraph with checkpointing
from langgraph.checkpoint.sqlite import SqliteSaver

checkpointer = SqliteSaver.from_conn_string("checkpoints.db")
app = graph.compile(checkpointer=checkpointer)

# Resume from a specific checkpoint
config = {"configurable": {"thread_id": "task-123"}}
result = app.invoke(input, config)
```

**Benefit:** Time-travel debugging. Rollback to any state. Resume after crashes.

## Cross-Session State

For agents that work across multiple sessions, state must be persisted externally:

- **Session handoff notes** — Structured markdown summarizing what happened
- **Knowledge base** — Searchable database of decisions, findings, preferences
- **Checkpoint DB** — Serialized graph state for resumable workflows

This is where tools like SAME provide value — they handle the write-side discipline of capturing what matters and the read-side retrieval of surfacing it when needed.

## See Also

- `research/memory/session-handoffs.md` — Cross-session state transfer
- `research/memory/persistent-memory-patterns.md` — Long-term memory
- `entities/langgraph.md` — LangGraph state management
