---
title: "SeedVaults Deep Dive"
tags: [seedvaults, seeds, templates, knowledge, sharing, community]
content_type: research
domain: same-getting-started
---

# SeedVaults Deep Dive

## What Are SeedVaults?

A SeedVault is a pre-built knowledge vault that you can install in one command. Instead of building your knowledge base from scratch, you start with expert-curated notes on a specific topic and grow from there.

Think of it like a starter template, but for knowledge instead of code.

## Available Seeds

```bash
same seed list
```

Some of the available seeds:

| Seed | Notes | What you get |
|------|:-----:|-------------|
| `claude-code-power-user` | 52 | Claude Code workflows and operational patterns |
| `ai-agent-architecture` | 58 | Agent design, orchestration, memory strategies |
| `personal-productivity-os` | 118 | GTD, time blocking, habit systems |
| `same-getting-started` | 18 | This seed -- the universal SAME on-ramp |

## Installing a Seed

```bash
same seed install claude-code-power-user
```

This downloads the seed, places it in your vault directory, and indexes all the notes. Your AI immediately has access to 50+ expert notes.

### Manual Install

```bash
git clone https://github.com/sgx-labs/seed-vaults.git
cd seed-vaults/seeds/claude-code-power-user
same init
same reindex
```

## How Seeds Are Structured

Every seed follows a consistent structure:

```
seed-name/
  bootstrap.md          # Pinned onboarding note
  CLAUDE.md             # Agent governance rules
  config.toml.example   # Configuration template
  hub/                  # Core concepts (5-7 overview notes)
  research/             # Deep dives (10-20 detailed notes)
  entities/             # Quick references (10-20 concept cards)
  decisions/            # Pre-populated decisions
  guides/               # Step-by-step how-tos
  sessions/             # Session handoff storage
  _archive/             # Archived originals
```

The key directories follow a cascade pattern:
1. **hub/** -- Start here. These are overviews.
2. **research/** -- Go deeper on specific questions.
3. **entities/** -- Quick reference cards for tools and concepts.
4. **guides/** -- Step-by-step instructions.

## The "Water the Seed" Protocol

Every seed includes an onboarding protocol called "Water the Seed." When you first activate a seed with your AI:

1. The AI silently scans your project to understand your situation
2. It presents an insight report showing what it learned
3. It asks targeted questions to personalize the seed
4. It recommends research topics based on your specific needs
5. It generates personalized notes and saves them to your vault

The result: a seed with 50 generic notes becomes a vault with 50 generic notes PLUS 10-20 notes specifically about YOUR project.

## Using Multiple Seeds

You can have multiple seeds in separate directories and search across all of them:

```bash
same search --all "rate limiting strategies"
```

Or via MCP:
```
search_across_vaults: query="rate limiting strategies"
```

This is powerful. Your "cloud architecture" seed might have notes about rate limiting that complement your "security audit" seed's perspective on the same topic.

## Creating Your Own Seed

See `guides/create-your-own-seed.md` for a full walkthrough. The short version:

1. Create the directory structure
2. Write hub notes for core concepts
3. Write research notes for deep dives
4. Add entity notes for quick reference
5. Include a bootstrap.md and CLAUDE.md
6. Test with `same reindex` and `same search`

## Sharing Seeds

Seeds are just folders of markdown files. Share them however you want:
- Push to GitHub
- Submit to the SeedVaults repository
- Share as a zip file
- Include in your project's documentation

## Next Steps

- Create your own seed: `guides/create-your-own-seed.md`
- Understand vault structure: `hub/vault-basics.md`
- Install your first seed: run `same seed list` to browse
