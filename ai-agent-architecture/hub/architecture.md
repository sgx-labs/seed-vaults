---
title: "Architecture — Agent Patterns and Orchestration"
tags: [architecture, patterns, orchestration, multi-agent, single-agent, ReAct]
content_type: hub
domain: engineering
---

# Architecture — Agent Patterns and Orchestration

## The Core Question

How do you structure an AI agent system? The answer depends on task complexity, reliability requirements, and how much you can afford to get wrong.

## Agent Architecture Spectrum

```
Simple ─────────────────────────────────── Complex
Prompt Chain → ReAct Loop → Plan-Execute → Multi-Agent
```

Most teams start too far right. A single ReAct agent handles 80% of real-world tasks. Multi-agent adds coordination overhead that only pays off for genuinely parallel or specialized workloads.

## Reasoning Patterns

| Pattern | How it works | Best for |
|---------|-------------|----------|
| ReAct | Think → Act → Observe → Repeat | Tool-using agents, most general tasks |
| Plan-and-Execute | Plan all steps first, then execute | Multi-step workflows with known structure |
| Reflexion | ReAct + self-critique + memory of past attempts | Tasks requiring iterative improvement |
| Tree of Thoughts | Explore multiple reasoning paths in parallel | Complex reasoning with uncertain approaches |

## Multi-Agent Patterns

| Pattern | Structure | When to use |
|---------|-----------|------------|
| Supervisor | Manager routes tasks to specialist workers | Diverse subtasks, clear specialization |
| Pipeline | Agent A output feeds Agent B input | Sequential processing stages |
| Debate | Multiple agents propose, critique, converge | High-stakes decisions needing verification |
| Scatter-Gather | Fan out to N agents, merge results | Parallelizable research or analysis |

## Decision Framework

**Use a single agent when:**
- The task fits in one context window
- You need one type of expertise
- Latency matters (multi-agent adds round trips)
- You are prototyping (start simple, evolve later)

**Use multi-agent when:**
- Subtasks genuinely benefit from different system prompts
- Work can be parallelized for speed
- You need isolation (one agent failing should not corrupt another)
- The task exceeds one context window

## Key Principle

> Start with the simplest architecture that could work. Add complexity only when you have evidence it is needed. Every agent you add is a new failure mode.

## Research Notes

- `research/architecture/single-vs-multi-agent.md` — Decision framework with examples
- `research/architecture/react-pattern.md` — ReAct implementation deep dive
- `research/architecture/plan-and-execute.md` — Plan-and-Execute vs ReAct tradeoffs
- `research/architecture/reflexion-pattern.md` — Self-improving agents with memory
- `research/architecture/multi-agent-communication.md` — Agent-to-agent coordination
- `research/architecture/agent-handoffs.md` — Delegation and handoff patterns
- `research/architecture/state-management.md` — Managing agent state across turns
