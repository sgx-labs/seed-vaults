# Indie Hacker Playbook — Governance

## Purpose

Knowledge seed for solo founders building and launching products. Search before generating — the answer is likely already here.

## Rules

1. **Search first.** Before answering a business question from scratch, search this vault.
2. **Don't modify research notes.** They are reference material. Add new notes if something is missing.
3. **Hub notes are entry points.** They link to detailed research notes. Start with hubs.
4. **Keep notes focused.** One question per research note. Under 200 lines.
5. **Entity files track current state.** Update when platforms change their rules.
6. **This is a playbook, not gospel.** Adapt patterns to your specific situation.
7. **Log decisions.** When making a project decision, log it in `decisions/log.md`.

## Directory Structure

```
indie-hacker-playbook/
├── CLAUDE.md                    # This file
├── bootstrap.md                 # Pinned — session orientation
├── config.toml.example          # SAME configuration template
├── hub/                         # Category index notes
│   ├── validation.md
│   ├── building.md
│   ├── launching.md
│   ├── pricing.md
│   ├── growth.md
│   └── operations.md
├── research/                    # Detailed guides
│   ├── validation/              # Idea validation patterns
│   ├── building/                # MVP and tech stack
│   ├── launching/               # Launch channel playbooks
│   ├── pricing/                 # Pricing strategy
│   ├── growth/                  # Distribution and marketing
│   └── operations/              # Solo founder operations
├── entities/                    # Living platform docs
├── templates/                   # Fill-in-the-blank templates
├── decisions/                   # Project decisions
└── sessions/                    # Session handoffs
```

## Content Types

| Type | Purpose | Notes |
|------|---------|-------|
| `hub` | Navigation indexes | Start here for any topic |
| `research` | Detailed how-to guides | One question per file |
| `entity` | Platform current state | Update when things change |
| `template` | Reusable frameworks | Fill in for your project |
| `decision` | Project choices | Log when you make decisions |

## Using SAME Search

```bash
# CLI search
same search "how to price my SaaS"

# MCP search
mcp__same__search_notes(query="product hunt launch checklist", top_k=5)

# Filtered search
mcp__same__search_notes_filtered(query="landing page", domain="business", top_k=5)
```
