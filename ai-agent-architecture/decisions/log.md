---
title: "Decision Log"
tags: [decisions, architecture, log]
content_type: decision
domain: engineering
---

# Decision Log

## AD-001: Start with single agent, evolve to multi-agent

**Status:** Accepted
**Date:** 2026-02-14

A single ReAct agent handles 80% of real-world tasks. Multi-agent systems add coordination overhead, debugging complexity, and failure modes. Start with one capable agent. Add specialization only when you have evidence that the single agent is struggling with specific subtask types or context window limits.

**Alternative considered:** Multi-agent from the start. Rejected because premature orchestration complexity is the most common mistake in agent architecture. You cannot debug multi-agent coordination issues if you do not yet understand single-agent behavior.

## AD-002: MCP for tool interfaces

**Status:** Accepted
**Date:** 2026-02-14

Use Model Context Protocol as the standard tool interface. MCP is provider-agnostic (works with Claude, GPT, Gemini), has a well-defined schema, and is supported by all major AI platforms. Define tools once as MCP servers, use them from any agent framework.

**Alternative considered:** Framework-specific tool definitions (LangChain tools, OpenAI function calling). Rejected because framework lock-in is expensive when the agent framework landscape is still evolving rapidly.

## AD-003: Structured output for critical paths

**Status:** Accepted
**Date:** 2026-02-14

Use JSON Schema validation for any agent output that feeds into downstream systems (API calls, database writes, file operations). Free-form text is fine for human-facing responses, but structured output prevents hallucinated field names, wrong types, and missing required data.

**Alternative considered:** Free-form output with post-processing. Rejected because regex parsing of LLM output is brittle and fails silently on edge cases.

## AD-004: Human-in-the-loop for destructive actions

**Status:** Accepted
**Date:** 2026-02-14

Agents propose, humans approve. Any action that deletes data, spends money, sends external communications, or modifies production systems must require explicit human confirmation. The approval step should show exactly what the agent intends to do in plain language.

**Alternative considered:** Fully autonomous agents with undo capability. Rejected because some actions are not reversible (sent emails, deleted data without backups, financial transactions).

## AD-005: Local memory first

**Status:** Accepted
**Date:** 2026-02-14

Use local storage (SQLite, filesystem) for agent memory before considering cloud solutions. Local-first means your agent works offline, keeps data private by default, has zero API costs for memory operations, and avoids latency for every knowledge retrieval. Cloud sync can be added later if needed.

**Alternative considered:** Cloud vector databases (Pinecone, Weaviate). Rejected as the default because they add latency, cost, and privacy concerns that most agent builders do not need initially. For team-shared agents at scale, cloud memory makes sense — but start local.
