---
title: "AI Agent Architecture — Governance"
content_type: hub
tags: [governance, rules, structure, search, vault-guide]
domain: engineering
pinned: true
---

# AI Agent Architecture — Governance

## Purpose

Knowledge seed for building production AI agents. Search before designing from scratch — the answer is likely already here.

## Rules

1. **Search first.** Before answering an agent architecture question, search this vault.
2. **Don't modify research notes.** They are reference material. Add new notes if something is missing.
3. **Hub notes are entry points.** They link to detailed research notes. Start with hubs.
4. **Keep notes focused.** One question per research note. Under 200 lines.
5. **Entity files track current state.** Update when framework versions change.
6. **Patterns are framework-agnostic where possible.** Framework-specific details go in research/ or entities/.
7. **Security notes are critical.** Always check reliability research before giving agents new capabilities.

## Directory Structure

```
ai-agent-architecture/
├── CLAUDE.md                    # This file
├── bootstrap.md                 # Pinned — session orientation
├── config.toml.example          # SAME configuration template
├── hub/                         # Category index notes
│   ├── architecture.md
│   ├── tools.md
│   ├── memory.md
│   ├── reliability.md
│   ├── deployment.md
│   └── frameworks.md
├── research/                    # Detailed guides
│   ├── architecture/            # Agent patterns, orchestration
│   ├── tools/                   # Tool design, MCP, function calling
│   ├── memory/                  # Memory systems, context management
│   ├── reliability/             # Error handling, guardrails, testing
│   ├── deployment/              # Production, monitoring, scaling
│   └── frameworks/              # Framework-specific guides
├── entities/                    # Living reference docs
├── decisions/                   # Architectural decisions
└── sessions/                    # Session handoffs
```

## Content Types

| Type | Purpose | Notes |
|------|---------|-------|
| `hub` | Navigation indexes | Start here for any topic |
| `research` | Detailed how-to guides | One question per file |
| `entity` | Framework/concept current state | Update when things change |
| `decision` | Architectural choices | Log when you make decisions |

## Using SAME Search

```bash
# CLI search
same search "how to handle agent errors in production"

# MCP search
mcp__same__search_notes(query="multi-agent coordination", top_k=5)

# Filtered search
mcp__same__search_notes_filtered(query="guardrails", domain="engineering", top_k=5)
```
