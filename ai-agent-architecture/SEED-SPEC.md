---
title: "AI Agent Architecture Seed — Build Specification"
content_type: template
tags: [seed-spec, build-plan, hub-categories, key-questions, entities]
domain: engineering
---

# AI Agent Architecture Seed

## Metadata
- **Name**: ai-agent-architecture
- **Type**: Knowledge
- **Audience**: Developers building AI agents with Claude Agent SDK, LangGraph, CrewAI, or similar frameworks
- **Note count target**: 60-80
- **Agent build time**: 3-4 hours

## Purpose
Patterns, anti-patterns, and architectural decisions for building production AI agents. Covers multi-agent orchestration, tool design, memory systems, error handling, and deployment. Your agent's reference library for building agents.

## Hub Categories

1. hub/architecture.md — Agent patterns, single vs multi-agent, orchestration
2. hub/tools.md — Tool design, MCP servers, function calling best practices
3. hub/memory.md — Persistent memory, context management, knowledge bases
4. hub/reliability.md — Error handling, retries, fallbacks, guardrails
5. hub/deployment.md — Running agents in production, monitoring, scaling
6. hub/frameworks.md — Claude Agent SDK, LangGraph, CrewAI, AutoGen comparison

## Key Questions

1. When should I use a single agent vs multi-agent architecture?
2. How do I design tools that agents can use effectively?
3. What are the patterns for agent-to-agent communication?
4. How do I handle agent errors and hallucinations in production?
5. How do I give agents persistent memory across sessions?
6. What's the right way to implement human-in-the-loop approval?
7. How do I manage context windows effectively in long-running agents?
8. What are the cost optimization strategies for agent systems?
9. How do I test agents (unit tests, integration tests, eval harnesses)?
10. How do I monitor agent behavior in production?
11. What are the security considerations for agent tool use?
12. How do I implement guardrails and safety constraints?
13. What's the difference between ReAct, Plan-and-Execute, and reflexion patterns?
14. How do I handle rate limits and API failures gracefully?
15. How do I design prompts that work reliably across model versions?
16. When should I use structured output vs free-form?
17. How do I implement agent handoffs and delegation?
18. What are the patterns for agent state management?
19. How do I debug agent behavior (tracing, logging, replay)?
20. How do I evaluate agent quality (metrics, benchmarks, A/B tests)?

## Entities

1. entities/claude-agent-sdk.md — SDK capabilities, patterns, limitations
2. entities/langgraph.md — Graph-based orchestration, state, checkpoints
3. entities/mcp-protocol.md — Model Context Protocol, tool schema, transport
4. entities/openrouter.md — Multi-model routing, pricing tiers, API
5. entities/context-window.md — Token limits by model, management strategies

## Decisions to Pre-Populate

1. AD-001: Start with single agent, evolve to multi — avoid premature complexity
2. AD-002: MCP for tool interfaces — standard protocol, works across providers
3. AD-003: Structured output for critical paths — JSON schema validation prevents hallucination
4. AD-004: Human-in-the-loop for destructive actions — agents propose, humans approve
5. AD-005: Local memory first — SAME/SQLite over cloud memory services

## CLAUDE.md Governance

- This seed is reference material for building agents — search before designing from scratch
- Patterns are framework-agnostic where possible, with framework-specific notes in research/
- Security notes are critical — always check research/security/ before giving agents new capabilities
- Entity files track the current state of frameworks (they change fast)

## Config Notes

```toml
[vault]
  path = "."
  skip_dirs = ["_Raw"]
  handoff_dir = "sessions"
  decision_log = "decisions/log.md"

[memory]
  max_token_budget = 1200
  max_results = 4
  composite_threshold = 0.65
```

## Why This Seed

- **Our adjacent audience** — agent builders are SAME's most natural users
- **High perceived value** — agent architecture is complex, curated knowledge saves weeks
- **Demonstrates SAME as agent memory** — the seed IS the use case
- **Fast-moving domain** — constant updates mean the seed stays relevant
- **Cross-promotes SAME** — every pattern recommends persistent memory (which is SAME)
