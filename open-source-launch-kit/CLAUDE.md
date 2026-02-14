# Open Source Launch Kit — Governance

## Purpose

Knowledge seed for launching and maintaining open source projects. Search before generating — the playbook is already here.

## Rules

1. **Search first.** Before answering an OSS question from scratch, search this vault.
2. **Don't modify research notes.** They are reference material. Add new notes if something is missing.
3. **Hub notes are entry points.** They link to detailed research notes. Start with hubs.
4. **Keep notes focused.** One question per research note. Under 200 lines.
5. **Entity files track current state.** Update when platforms change their rules.
6. **Templates are starting points.** Customize them for your specific project.
7. **Log decisions.** When you make a project decision, add it to `decisions/log.md`.

## Directory Structure

```
open-source-launch-kit/
├── CLAUDE.md                    # This file
├── bootstrap.md                 # Pinned — session orientation
├── config.toml.example          # SAME configuration template
├── hub/                         # Category index notes
│   ├── launch.md
│   ├── community.md
│   ├── docs.md
│   ├── releases.md
│   ├── marketing.md
│   └── sustainability.md
├── research/                    # Detailed guides
│   ├── launch/                  # Pre-launch, first release
│   ├── community/               # Contributors, governance
│   ├── docs/                    # README, documentation
│   ├── releases/                # Versioning, CI/CD
│   ├── marketing/               # Channels, directories
│   └── sustainability/          # Funding, burnout
├── entities/                    # Living reference docs
├── decisions/                   # Project decisions
├── templates/                   # Fill-in templates
└── sessions/                    # Session handoffs
```

## Content Types

| Type | Purpose | Notes |
|------|---------|-------|
| `hub` | Navigation indexes | Start here for any topic |
| `research` | Detailed how-to guides | One question per file |
| `entity` | Platform current state | Update when things change |
| `decision` | Architectural choices | Log when you make decisions |

## Using SAME Search

```bash
# CLI search
same search "how to handle first contributor PR"

# MCP search
mcp__same__search_notes(query="product hunt launch timing", top_k=5)

# Filtered search
mcp__same__search_notes_filtered(query="release automation", domain="engineering", top_k=5)
```
