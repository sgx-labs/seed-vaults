---
title: "When Should I Use Single vs Multi-Agent Architecture?"
tags: [architecture, single-agent, multi-agent, decision-framework, coordination-tax, specialization, context-overflow]
content_type: research
domain: engineering
---

# When Should I Use Single vs Multi-Agent Architecture?

## The Question

When is a single agent sufficient, and when do you actually need multiple agents?

## The Short Answer

Start with one agent. Most people over-engineer this. A single capable model with good tools handles the vast majority of real-world tasks. Multi-agent is a scaling strategy, not a starting point.

## Single Agent: When and Why

A single agent is the right choice when:

- **One expertise domain.** The task does not require fundamentally different system prompts.
- **Sequential workflow.** Steps happen in order, not in parallel.
- **Fits in context.** The full task state fits within one context window.
- **Latency matters.** Each agent handoff adds a round-trip to the LLM.
- **You are still learning.** Understand one agent's behavior before coordinating many.

**Example:** A coding agent that reads files, writes code, runs tests, and commits. One agent, many tools. No orchestration needed.

## Multi-Agent: When and Why

Multiple agents become necessary when:

- **Genuine specialization.** Different subtasks need different system prompts, models, or tool sets. A research agent needs web search; a coding agent needs file access.
- **Parallel execution.** Subtasks are independent and can run simultaneously for speed.
- **Context isolation.** One agent failing or going off-track should not corrupt another agent's work.
- **Context overflow.** The full task state exceeds one context window.

**Example:** A documentation pipeline where Agent A researches APIs, Agent B writes docs, and Agent C reviews for accuracy. Each has different tools and prompts.

## The Coordination Tax

Every agent you add costs you:

| Cost | Impact |
|------|--------|
| Latency | Each handoff = another LLM call |
| Tokens | Shared context must be serialized between agents |
| Debugging | Multi-agent traces are harder to follow |
| Failure modes | Agent A fails → does Agent B know? |
| Prompt engineering | N agents = N system prompts to maintain |

## Decision Checklist

```
[ ] Can one agent do this with the right tools? → Try single first
[ ] Are subtasks truly parallel? → Multi-agent may help speed
[ ] Do subtasks need different system prompts? → Multi-agent adds value
[ ] Does the task exceed one context window? → Must split
[ ] Is coordination complexity worth the benefit? → Be honest
```

## Common Mistakes

1. **Splitting by function, not by need.** Creating a "search agent" and "write agent" when one agent can search AND write is unnecessary complexity.
2. **Assuming multi = better.** LangChain's own benchmarks show single-agent outperforms multi-agent on many tasks when the single agent has good tools.
3. **No shared state strategy.** Multi-agent without a plan for state sharing leads to agents repeating work or contradicting each other.

## See Also

- `hub/architecture.md` — Architecture overview
- `research/architecture/multi-agent-communication.md` — Agent coordination patterns
- `research/architecture/agent-handoffs.md` — Delegation patterns
