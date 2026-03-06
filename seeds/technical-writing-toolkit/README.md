---
title: "Technical Writing Toolkit"
description: "50+ notes on documentation, technical writing, and creating knowledge that compounds -- including how to build more SAME seeds"
keywords: [technical-writing, documentation, readme, adr, rfc, runbook, changelog, style-guide, docs-as-code, diataxis, markdown, seedvault]
install: "same seed install technical-writing-toolkit"
engine: "https://github.com/sgx-labs/statelessagent"
---

# Technical Writing Toolkit

A comprehensive seed for engineers who write documentation, and for anyone building SAME SeedVaults. Every note in this seed practices what it preaches -- the writing itself demonstrates the principles it teaches.

## What's Inside

- 50+ notes covering documentation strategy, writing craft, tooling, and meta-skills
- Hub notes for core documentation types (READMEs, ADRs, RFCs, runbooks, changelogs)
- Research notes on docs-as-code, tooling, internationalization, and documentation debt
- Entity notes for quick-reference lookups (Markdown syntax, Mermaid diagrams, style checklists)
- Guide notes with step-by-step walkthroughs for common documentation tasks
- A self-referential SeedVault creation guide that closes the flywheel

## Who It's For

Software engineers who write documentation (which should be all of them). Technical writers embedded in engineering teams. Anyone building SAME SeedVaults. Assumes basic Markdown knowledge and experience working in a codebase.

## Prerequisites

- [SAME](https://github.com/sgx-labs/statelessagent) installed
- An embedding provider configured (Ollama, OpenRouter, or any OpenAI-compatible API)

## Install

```bash
same seed install technical-writing-toolkit
```

Or clone manually:

```bash
git clone https://github.com/sgx-labs/technical-writing-toolkit.git
cd technical-writing-toolkit
same init
same reindex
```

## Getting Started

1. Run `same init` to configure your embedding provider
2. Run `same reindex` to index all seed notes
3. Start a conversation with your AI agent -- the bootstrap note guides first-session onboarding
4. Answer the targeted questions to personalize the seed to your project
5. Pick research topics from the cascade to grow your vault

## Directory Structure

```
technical-writing-toolkit/
  bootstrap.md          # Pinned onboarding note (read every session)
  CLAUDE.md             # Agent governance rules
  config.toml.example   # Provider-agnostic SAME config template
  hub/                  # Core documentation type overviews -- start here
  research/             # Deep dives on tooling, strategy, and craft
  entities/             # Quick-reference docs (Markdown, Mermaid, checklists)
  guides/               # Step-by-step how-tos
  decisions/            # Decision templates and log
  sessions/             # Session handoff notes
  _archive/             # Safely archived originals from merge process
```

## The Flywheel

This seed is self-referential. It teaches you how to write great documentation, and one of the things it documents is how to build SAME SeedVaults. Every note demonstrates the writing principles it teaches. The better you understand the content, the better seeds you can create, which creates more content to learn from.

See `research/seedvault-creation-guide.md` and `guides/building-a-seedvault.md` to start the loop.

## License

Content licensed under [CC BY-SA 4.0](https://creativecommons.org/licenses/by-sa/4.0/).
