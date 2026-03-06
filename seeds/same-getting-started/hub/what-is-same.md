---
title: "What is SAME?"
tags: [same, overview, introduction, memory, ai-agents, getting-started]
content_type: hub
domain: same-getting-started
---

# What is SAME?

## The Problem

Every time you start a new session with an AI coding agent -- Claude Code, Cursor, Windsurf -- it forgets everything. Your decisions, your preferences, the context from yesterday's debugging session, the architecture you agreed on last week. Gone.

You end up re-explaining the same things over and over. "We decided to use JWT for auth." "The database schema looks like this." "Don't touch that file, it's legacy." It's like working with a brilliant colleague who has amnesia.

## The Solution

SAME (Stateless Agent Memory Engine) gives your AI persistent memory. It works by:

1. **Indexing your notes** -- You write markdown files (or let the AI write them). SAME turns them into searchable embeddings stored in a local SQLite database.
2. **Surfacing context automatically** -- When your AI starts a session, SAME injects relevant notes, recent decisions, and the last session's handoff. Your AI picks up where it left off.
3. **Recording decisions** -- When you make a project decision, SAME logs it. Future sessions see that decision and don't re-litigate it.
4. **Creating handoffs** -- At the end of each session, SAME captures what was done, what's pending, and what the AI should know next time.

## What It's Not

- **Not a vector database** -- SAME uses SQLite. No Pinecone, no Weaviate, no infrastructure.
- **Not a cloud service** -- Everything runs on your machine. No accounts, no telemetry, no data leaving your laptop.
- **Not tied to one tool** -- SAME works with Claude Code, Cursor, Windsurf, and any MCP-compatible client.
- **Not complicated** -- One binary, one command to set up, under 60 seconds to get running.

## How It Feels

Without SAME:
> "Can you remind me what we decided about the database migration?"

With SAME:
> Your AI already knows. It read the decision log, saw the handoff from yesterday, and picked up the migration where you left off.

The goal is to make your AI feel like it has been working with you for months, even though every session starts fresh.

## Key Concepts

- **Vault** -- A folder of markdown notes that SAME indexes and searches. See `hub/vault-basics.md`.
- **MCP** -- Model Context Protocol, how SAME gives tools to AI agents. See `research/how-mcp-works.md`.
- **SeedVault** -- A pre-built vault template you can install in one command. See `research/seed-vaults-deep-dive.md`.
- **Handoff** -- A session summary that bridges the gap between sessions. See `hub/session-memory.md`.

## Next Steps

- Ready to install? See `guides/install-same.md`
- Want to understand vaults? See `hub/vault-basics.md`
- Want to see it work? Run `same demo` after installing
