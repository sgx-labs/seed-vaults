---
title: "Bootstrap — AI Agent Architecture Quick Start"
tags: [bootstrap, onboarding, session-start, critical]
content_type: hub
domain: engineering
pinned: true
---

# Bootstrap — AI Agent Architecture Quick Start

## 1. What This Is

A curated knowledge base for building production AI agents. Covers architecture patterns, tool design, memory systems, reliability engineering, deployment, and framework selection. Search for what you need, get a battle-tested answer instantly.

This seed is itself an example of agent memory — a knowledge base about agent architecture, searchable by agents.

## 2. Where To Find Things

| Need | Search for | Or read directly |
|------|-----------|-----------------|
| Architecture patterns | `single agent multi-agent orchestration` | `hub/architecture.md` |
| Tool design | `tool design MCP function calling` | `hub/tools.md` |
| Memory systems | `persistent memory context management` | `hub/memory.md` |
| Reliability | `error handling retries guardrails` | `hub/reliability.md` |
| Deployment | `production monitoring scaling` | `hub/deployment.md` |
| Framework comparison | `Claude SDK LangGraph CrewAI` | `hub/frameworks.md` |
| Claude Agent SDK | `Claude SDK patterns capabilities` | `entities/claude-agent-sdk.md` |
| MCP protocol | `MCP tool schema transport` | `entities/mcp-protocol.md` |
| Context windows | `token limits context management` | `entities/context-window.md` |

## 3. How To Use This Seed

```bash
# Search for anything
same search "how do I test agents"

# Or via MCP
mcp__same__search_notes(query="multi-agent orchestration patterns", top_k=5)

# Find related notes
mcp__same__find_similar_notes(path="research/architecture/single-vs-multi-agent.md", top_k=3)
```

## 4. Key Concepts

- **ReAct** — Reason-then-Act loop. The most common agent pattern: think, use a tool, observe, repeat.
- **MCP** — Model Context Protocol. Standard interface for giving agents tools. Adopted by all major providers.
- **Guardrails** — Safety constraints that prevent agents from taking harmful actions. Not optional in production.
- **Context engineering** — Curating the minimal, high-signal set of tokens the model sees each step.
- **Agent harness** — The infrastructure around the model: tool orchestration, state management, lifecycle control.
- **Eval harness** — Testing infrastructure that measures agent quality across many trials.

## 5. Content Organization

- `hub/` — Category overviews. Start here for any topic.
- `research/` — Detailed guides. One question per file. Organized by hub category.
- `entities/` — Living docs about frameworks and concepts that change frequently.
- `decisions/` — Architectural decisions with rationale and alternatives.
