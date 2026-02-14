---
title: "What Are the Patterns for Agent-to-Agent Communication?"
tags: [multi-agent, communication, coordination, message-passing, shared-state, blackboard, supervisor, handoff]
content_type: research
domain: engineering
---

# What Are the Patterns for Agent-to-Agent Communication?

## The Question

When you have multiple agents, how do they share information and coordinate work?

## Communication Patterns

### 1. Shared State (Blackboard)

All agents read from and write to a shared state object. No direct messaging between agents.

```python
# LangGraph shared state
class ProjectState(TypedDict):
    requirements: str
    research_results: list
    draft_code: str
    test_results: list

# Each agent reads what it needs and writes its output
def research_agent(state: ProjectState) -> ProjectState:
    results = search(state["requirements"])
    return {"research_results": results}

def coding_agent(state: ProjectState) -> ProjectState:
    code = generate_code(state["requirements"], state["research_results"])
    return {"draft_code": code}
```

**Pros:** Simple, decoupled, easy to debug (inspect state at any point).
**Cons:** State object can grow large. Agents must agree on state schema.

### 2. Message Passing

Agents send messages directly to each other. Resembles human conversation.

```python
# CrewAI delegation
manager_agent.delegate(
    task="Review this code for security issues",
    agent=security_agent,
    context=code_to_review,
)
```

**Pros:** Natural mental model. Agents can negotiate and clarify.
**Cons:** Conversation overhead. Hard to track who said what.

### 3. Supervisor Routing

A supervisor agent decides which worker agent handles each subtask.

```
User Task → Supervisor → routes to → Worker A (research)
                                    → Worker B (coding)
                                    → Worker C (testing)
         ← Supervisor ← aggregates ← results from all workers
```

**Pros:** Centralized control. Easy to add/remove workers.
**Cons:** Supervisor is a bottleneck. Must understand all subtask types.

### 4. Handoff (Transfer of Control)

One agent transfers the entire conversation to another agent. Used in OpenAI Agents SDK.

```python
# Agent handoff
transfer_to_agent(
    target="specialist_agent",
    context="User needs help with database migration",
    conversation_history=messages,
)
```

**Pros:** Clean ownership. One agent active at a time.
**Cons:** Context must be serialized. No parallel execution.

## Choosing a Pattern

| Pattern | Best for | Framework support |
|---------|----------|------------------|
| Shared state | Parallel pipelines, LangGraph workflows | LangGraph (native) |
| Message passing | Collaborative teams, debate patterns | CrewAI (native) |
| Supervisor routing | Task decomposition, diverse specialists | LangGraph, CrewAI |
| Handoff | Customer service, escalation paths | OpenAI Agents SDK |

## Common Pitfall: Over-Communicating

Agents that pass entire conversation histories to each other waste tokens and degrade quality. Instead:
- Pass **summaries**, not transcripts
- Pass **structured outputs**, not raw text
- Pass **what the next agent needs**, not everything the previous agent did

## See Also

- `research/architecture/single-vs-multi-agent.md` — When to use multi-agent
- `research/architecture/agent-handoffs.md` — Handoff and delegation patterns
- `research/architecture/state-management.md` — State management deep dive
