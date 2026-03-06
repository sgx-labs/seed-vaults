---
title: "SAME Getting Started"
description: "18+ notes to go from zero to a working SAME vault with MCP integration in minutes"
keywords: [same, stateless-agent, getting-started, mcp, vault, ai-memory, claude-code, cursor, setup, tutorial]
install: "same seed install same-getting-started"
engine: "https://github.com/sgx-labs/statelessagent"
---

# SAME Getting Started

Your complete on-ramp to SAME (Stateless Agent Memory Engine) -- from zero to a working vault in minutes.

## What's Inside

- 18 notes covering installation, core concepts, MCP integration, workflows, and SeedVaults
- Hub notes for orientation across SAME's key features
- Research notes with deep dives on MCP, agent handoffs, and SeedVaults
- Entity notes on configuration, CLAUDE.md, and the MCP server
- Guides for installation, Claude Code integration, daily workflows, and creating your own seeds
- An example decision document showing the format

## Who It's For

Anyone new to SAME -- whether you're a solo developer trying to give your AI persistent memory, or a team lead setting up shared knowledge bases. No prior experience with SAME or MCP required.

## Prerequisites

- [SAME](https://github.com/sgx-labs/statelessagent) installed
- An embedding provider configured (Ollama, OpenRouter, or any OpenAI-compatible API) -- or use keyword search with no dependencies at all

## Install

```bash
same seed install same-getting-started
```

Or clone manually:

```bash
git clone https://github.com/sgx-labs/seed-vaults.git
cd seed-vaults/seeds/same-getting-started
same init
same reindex
```

## Getting Started

1. Run `same init` to configure your embedding provider
2. Run `same reindex` to index all seed notes
3. Start a conversation with your AI agent -- the bootstrap note will guide first-session onboarding
4. Answer the targeted questions to personalize the seed to your situation
5. Pick research topics from the cascade menu to grow your vault

## Directory Structure

```
same-getting-started/
  bootstrap.md          # Pinned onboarding note (read every session)
  CLAUDE.md             # Agent governance rules
  config.toml.example   # Provider-agnostic SAME config template
  hub/                  # Core concepts -- start here for any topic
  research/             # Deep dives on MCP, agents, and SeedVaults
  entities/             # Quick references for config, CLAUDE.md, MCP
  decisions/            # Decision log with example entry
  guides/               # Step-by-step how-to guides
  sessions/             # Session handoff notes
  _archive/             # Safely archived originals from merge process
```

## Customizing This Seed

This seed ships with general-purpose SAME knowledge. To make it yours:

- **Water the Seed** -- your agent's Research Cascade will generate notes specific to your project
- **Add entity files** -- track tools, services, and concepts you use daily
- **Log decisions** -- every architectural choice gets recorded for future sessions
- **Create handoffs** -- session continuity prevents knowledge loss

## License

Content licensed under [CC BY-SA 4.0](https://creativecommons.org/licenses/by-sa/4.0/).
