# [Seed Name]

[One sentence: what this seed provides to the user.]

## What's Inside

- [N] notes covering [topic area 1], [topic area 2], and [topic area 3]
- Hub notes for orientation across key topics
- Research notes with detailed guides and techniques
- Entity notes on tools, concepts, and domain objects
- Decisions log for architectural choices

## Who It's For

[One to two sentences describing the target audience and what level of experience they should have.]

## Prerequisites

- [SAME](https://github.com/sgx-labs/statelessagent) installed
- An embedding provider configured (Ollama, OpenRouter, or any OpenAI-compatible API)

## Install

```bash
same seed install [seed-name]
```

Or clone manually:

```bash
git clone https://github.com/[org]/[seed-name].git
cd [seed-name]
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
[seed-name]/
  bootstrap.md          # Pinned onboarding note (read every session)
  CLAUDE.md             # Agent governance rules
  config.toml.example   # Provider-agnostic SAME config template
  hub/                  # Category overviews -- start here for any topic
  research/             # Detailed guides, one question per file
  entities/             # Living docs about tools and concepts
  decisions/            # Architectural decision log
  sessions/             # Session handoff notes
  _Raw/                 # Unprocessed source material (skipped by indexer)
  _archive/             # Safely archived originals from merge process
```

## Customizing This Seed

This seed ships with general-purpose knowledge. To make it yours:

- **Water the Seed** -- your agent's Research Cascade will generate notes specific to your project
- **Add entity files** -- track tools, services, and concepts you use daily
- **Log decisions** -- every architectural choice gets recorded for future sessions
- **Create handoffs** -- session continuity prevents knowledge loss

## License

Content licensed under [CC BY-SA 4.0](https://creativecommons.org/licenses/by-sa/4.0/).
