---
title: "SAME Getting Started -- Seed Spec"
tags: [seed-spec, planning, structure, same, getting-started]
content_type: hub
domain: same-getting-started
---

# SAME Getting Started -- Seed Spec

## Metadata
- **Name**: same-getting-started
- **Type**: Knowledge
- **Audience**: Developers new to SAME who want persistent AI memory
- **Note count target**: 18-25
- **Agent build time**: 1-2 hours

## Purpose

This seed answers: "I just heard about SAME -- how do I get started and what can it do for me?" It takes a user from zero knowledge to a working vault with MCP integration, daily workflows, and the confidence to create their own seeds.

## Hub Categories

1. hub/what-is-same.md -- Plain English explanation of SAME, the problem it solves
2. hub/vault-basics.md -- What a vault is, directory structure, how files work together
3. hub/search-and-discovery.md -- How to search your vault, tips for effective queries
4. hub/session-memory.md -- How SAME gives AI agents persistent memory across sessions
5. hub/your-first-vault.md -- Step-by-step walkthrough: create, add notes, search

## Key Questions (agent research targets)

1. What is SAME and why would I use it?
2. How do I install SAME on my machine?
3. What is a vault and how is it organized?
4. How do I search my vault effectively?
5. How does session memory work across AI sessions?
6. What is MCP and how does SAME use it?
7. How do I connect SAME to Claude Code?
8. How do multi-agent handoffs work?
9. What are SeedVaults and how do I use them?
10. What does a realistic daily workflow with SAME look like?
11. How do I create and share my own SeedVault?
12. What configuration options does SAME support?

## Entities

1. entities/mcp-server.md -- Quick reference for the MCP server and its 12 tools
2. entities/claude-md.md -- What CLAUDE.md files are and why they matter
3. entities/config-toml.md -- Configuration reference for .same/config.toml

## Decisions to Pre-Populate

1. AD-001: Use hub-first navigation pattern (hub -> research -> entities) for content organization

## Water the Seed Protocol

See CLAUDE.md for the full Research Cascade protocol. Key behaviors:

- Agent scans project before speaking
- Presents insight report showing understanding
- Asks targeted questions to personalize
- Archives before overwriting
- Recommends research topics ranked by impact
- Saves results as vault notes with proper frontmatter

## Config Notes

```toml
[vault]
  path = "."
  skip_dirs = ["_Raw", "_archive"]
  handoff_dir = "sessions"
  decision_log = "decisions/log.md"

[memory]
  composite_threshold = 0.65
  max_results = 4
```

## Quality Checklist
- [ ] Every note under 200 lines
- [ ] YAML frontmatter on all notes (title, tags, content_type, domain)
- [ ] bootstrap.md with pinned: true and "Water the Seed" section
- [ ] CLAUDE.md with merge protocol and SAME showcase behaviors
- [ ] config.toml.example (provider-agnostic)
- [ ] No PII, no API keys, no secrets
- [ ] `_archive/` in skip_dirs
- [ ] `same reindex` produces 0 errors
- [ ] `same search` returns relevant results for 5 test queries
- [ ] Key questions are answerable by searching the vault
- [ ] SAME tools are demonstrated naturally in CLAUDE.md examples
