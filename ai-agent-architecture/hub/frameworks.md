---
title: "Frameworks — Comparison and Selection Guide"
tags: [frameworks, Claude-SDK, LangGraph, CrewAI, AutoGen, comparison]
content_type: hub
domain: engineering
---

# Frameworks — Comparison and Selection Guide

## The Framework Landscape (2026)

The agent framework space has consolidated around a few serious options. The right choice depends on your use case, team, and how much control you need.

## Framework Comparison

| Framework | Language | Architecture | Best for | Complexity |
|-----------|----------|-------------|----------|-----------|
| Claude Agent SDK | Python/TS | Loop-based, tool-augmented | Claude-native agents, coding agents | Low |
| LangGraph | Python/JS | Graph-based state machine | Complex workflows, custom orchestration | Medium |
| CrewAI | Python | Role-based crews | Multi-agent teams, defined processes | Medium |
| OpenAI Agents SDK | Python | Handoff-based | OpenAI-native, simple multi-agent | Low |
| AutoGen/AG2 | Python | Conversation-based | Research, multi-turn collaboration | High |

## Selection Decision Tree

```
Do you need multi-agent orchestration?
├── No → Are you using Claude?
│        ├── Yes → Claude Agent SDK
│        └── No → OpenAI Agents SDK or LangGraph
└── Yes → Do agents need custom state machines?
          ├── Yes → LangGraph
          └── No → Do agents have clear roles?
                   ├── Yes → CrewAI
                   └── No → LangGraph
```

## Key Differentiators

**Claude Agent SDK** — Minimal abstraction. The agent IS the model with tools. Excellent for coding agents and tasks where one capable model is enough. Smallest learning curve.

**LangGraph** — Maximum control. You define the graph: nodes (agents/functions), edges (transitions), state (shared context). Best when you need deterministic control flow with LLM decision points.

**CrewAI** — Role-oriented. You define agents by role, backstory, and goals. The framework handles coordination. Best when your problem maps naturally to a team of specialists.

**OpenAI Agents SDK** — Handoff-centric. Agents can transfer control to other agents. Clean API, simple mental model. Replaced the experimental Swarm framework in March 2025.

## The Build-vs-Framework Decision

**Build your own when:**
- You need tight integration with existing systems
- Framework abstractions hide things you need to control
- You are building a product (not a tool) and need full ownership
- Your use case is simple enough that a framework adds overhead

**Use a framework when:**
- You want to prototype quickly
- The framework's opinions match your architecture
- You need features you do not want to build (checkpoints, state, replay)
- Your team benefits from the framework's conventions

## Research Notes

- `research/frameworks/claude-sdk-patterns.md` — Building with Claude Agent SDK
- `research/frameworks/langgraph-patterns.md` — LangGraph architecture guide
- `research/frameworks/crewai-patterns.md` — CrewAI multi-agent patterns
- `research/frameworks/build-vs-framework.md` — When to roll your own
- `research/frameworks/framework-migration.md` — Migrating between frameworks
