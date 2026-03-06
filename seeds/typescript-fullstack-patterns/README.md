---
title: "TypeScript Fullstack Patterns"
description: "60+ notes on TypeScript, Next.js, React Server Components, Prisma/Drizzle, authentication, testing, and production fullstack patterns"
keywords: [typescript, nextjs, react, prisma, drizzle, tailwind, zod, fullstack, app-router, server-components, authentication, testing, deployment]
install: "same seed install typescript-fullstack-patterns"
engine: "https://github.com/sgx-labs/statelessagent"
---

# TypeScript Fullstack Patterns

A production-grade reference seed for TypeScript fullstack developers building with Next.js, React, Prisma/Drizzle, and modern tooling. Not tutorials -- battle-tested patterns from real applications.

## What's Inside

- 60+ notes covering TypeScript patterns, Next.js App Router, database design, auth, testing, and deployment
- Hub notes for orientation across 15 core pattern areas
- Research notes with deep dives into caching, multi-tenancy, Stripe, AI integration, and more
- Entity notes for quick-reference lookups (utility types, file conventions, hooks)
- Guide notes with step-by-step how-tos for common fullstack tasks
- Decision templates for framework and database choices

## Who It's For

TypeScript fullstack developers building production applications. Useful whether you're starting a new Next.js project or scaling an existing one. Assumes working knowledge of TypeScript, React, and Node.js.

## Prerequisites

- [SAME](https://github.com/sgx-labs/statelessagent) installed
- An embedding provider configured (Ollama, OpenRouter, or any OpenAI-compatible API)

## Install

```bash
same seed install typescript-fullstack-patterns
```

Or clone manually:

```bash
git clone https://github.com/sgx-labs/typescript-fullstack-patterns.git
cd typescript-fullstack-patterns
same init
same reindex
```

## Getting Started

1. Run `same init` to configure your embedding provider
2. Run `same reindex` to index all seed notes
3. Start a conversation with your AI agent -- the bootstrap note will guide first-session onboarding
4. Answer the targeted questions to personalize the seed to your stack
5. Pick research topics from the cascade menu to grow your vault

## Directory Structure

```
typescript-fullstack-patterns/
  bootstrap.md          # Pinned onboarding note (read every session)
  CLAUDE.md             # Agent governance rules
  config.toml.example   # Provider-agnostic SAME config template
  hub/                  # Core pattern overviews -- start here for any topic
  research/             # Deep dives, one topic per file
  entities/             # Quick-reference docs (utility types, hooks, conventions)
  guides/               # Step-by-step how-tos
  decisions/            # Architectural decision templates and log
  sessions/             # Session handoff notes
  _archive/             # Safely archived originals from merge process
```

## Customizing This Seed

This seed ships with general-purpose fullstack TypeScript knowledge. To make it yours:

- **Water the Seed** -- your agent's Research Cascade will generate notes specific to your stack
- **Add entity files** -- track your specific components, hooks, and middleware
- **Log decisions** -- every architectural choice gets recorded for future sessions
- **Create handoffs** -- session continuity prevents knowledge loss

## License

Content licensed under [CC BY-SA 4.0](https://creativecommons.org/licenses/by-sa/4.0/).
