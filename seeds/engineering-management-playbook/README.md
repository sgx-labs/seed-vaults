---
title: "Engineering Management Playbook"
description: "60+ notes on 1-on-1s, hiring, performance, team health, and engineering leadership"
keywords: [engineering-management, leadership, 1-on-1, hiring, performance-review, team-health, delegation, incident-management, onboarding, metrics]
install: "same seed install engineering-management-playbook"
engine: "https://github.com/sgx-labs/statelessagent"
---

# Engineering Management Playbook

A comprehensive knowledge seed for engineering managers at every level -- from first-time tech leads to seasoned VPs. Covers the full spectrum of people management, technical leadership, organizational design, and operational excellence.

## What's Inside

- 60 notes covering frameworks, deep dives, quick references, and step-by-step guides
- Hub notes for 15 core management frameworks you'll use weekly
- Research notes with deep dives into scaling, culture, compensation, and organizational change
- Entity notes for quick-reference lookups (levels matrix, interview banks, OKR examples)
- Guide notes with step-by-step how-tos for your first 1-on-1, writing promo packets, and more
- Decision templates for team structure and process changes

## Who It's For

Engineering managers, tech leads, directors, and VPs. Useful whether you're running your first 1-on-1 or restructuring a 50-person org. Assumes you have some engineering background and are either managing people or about to start.

## Prerequisites

- [SAME](https://github.com/sgx-labs/statelessagent) installed
- An embedding provider configured (Ollama, OpenRouter, or any OpenAI-compatible API)

## Install

```bash
same seed install engineering-management-playbook
```

Or clone manually:

```bash
git clone https://github.com/sgx-labs/engineering-management-playbook.git
cd engineering-management-playbook
same init
same reindex
```

## Getting Started

1. Run `same init` to configure your embedding provider
2. Run `same reindex` to index all seed notes
3. Start a conversation with your AI agent -- the bootstrap note will guide first-session onboarding
4. Answer the targeted questions to personalize the seed to your team's situation
5. Pick research topics from the cascade menu to grow your vault

## Directory Structure

```
engineering-management-playbook/
  bootstrap.md          # Pinned onboarding note (read every session)
  CLAUDE.md             # Agent governance rules
  config.toml.example   # Provider-agnostic SAME config template
  hub/                  # Core management frameworks -- start here for any topic
  research/             # Deep dives, one topic per file
  entities/             # Quick-reference docs (levels, interview banks, templates)
  guides/               # Step-by-step how-tos
  decisions/            # Decision templates and log
  sessions/             # Session handoff notes
  _archive/             # Safely archived originals from merge process
```

## Customizing This Seed

This seed ships with general-purpose engineering management knowledge. To make it yours:

- **Water the Seed** -- your agent's Research Cascade will generate notes specific to your team
- **Add entity files** -- track your specific team members, processes, and cadences
- **Log decisions** -- every management decision gets recorded for future sessions
- **Create handoffs** -- session continuity prevents context loss between conversations

## License

Content licensed under [CC BY-SA 4.0](https://creativecommons.org/licenses/by-sa/4.0/).
