---
title: "API Design Patterns"
description: "53+ notes on REST, GraphQL, gRPC, authentication, rate limiting, and API best practices"
keywords: [api, rest, graphql, grpc, authentication, oauth2, rate-limiting, pagination, versioning, error-handling, openapi]
install: "same seed install api-design-patterns"
engine: "https://github.com/sgx-labs/statelessagent"
---

# API Design Patterns

A comprehensive reference seed for backend developers building APIs. Covers REST, GraphQL, gRPC, authentication, authorization, rate limiting, versioning, error handling, and dozens of production patterns.

## What's Inside

- 53 notes covering API design fundamentals, deep-dive research, quick references, and practical guides
- Hub notes for orientation across 15 core pattern areas
- Research notes with deep dives into OAuth2, circuit breakers, event-driven APIs, and more
- Entity notes for quick-reference lookups (status codes, headers, tools)
- Guide notes with step-by-step how-tos for common API tasks
- Decision templates for architectural choices

## Who It's For

Backend developers designing or maintaining APIs. Useful whether you're building your first REST API or evaluating GraphQL vs gRPC for a microservices migration. Assumes basic HTTP and programming knowledge.

## Prerequisites

- [SAME](https://github.com/sgx-labs/statelessagent) installed
- An embedding provider configured (Ollama, OpenRouter, or any OpenAI-compatible API)

## Install

```bash
same seed install api-design-patterns
```

Or clone manually:

```bash
git clone https://github.com/sgx-labs/api-design-patterns.git
cd api-design-patterns
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
api-design-patterns/
  bootstrap.md          # Pinned onboarding note (read every session)
  CLAUDE.md             # Agent governance rules
  config.toml.example   # Provider-agnostic SAME config template
  hub/                  # Core pattern overviews -- start here for any topic
  research/             # Deep dives, one topic per file
  entities/             # Quick-reference docs (status codes, headers, tools)
  guides/               # Step-by-step how-tos
  decisions/            # Architectural decision templates and log
  sessions/             # Session handoff notes
  _archive/             # Safely archived originals from merge process
```

## Customizing This Seed

This seed ships with general-purpose API knowledge. To make it yours:

- **Water the Seed** -- your agent's Research Cascade will generate notes specific to your API
- **Add entity files** -- track your specific endpoints, services, and middleware
- **Log decisions** -- every API design choice gets recorded for future sessions
- **Create handoffs** -- session continuity prevents knowledge loss

## License

Content licensed under [CC BY-SA 4.0](https://creativecommons.org/licenses/by-sa/4.0/).
