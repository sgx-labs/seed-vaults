---
title: "Decision Log"
tags: [decisions, architecture, log, governance, claude-md, hooks, mcp, batching]
content_type: decision
domain: engineering
---

# Decision Log

## AD-001: Use CLAUDE.md for project governance

**Status:** Accepted
**Date:** 2026-02-14

Version-controlled project instructions that Claude reads every session. Keeps governance in git where it belongs. Team members share the same rules. Better than verbal instructions or one-off prompts that get lost.

**Alternative considered:** Relying on session-by-session instructions. Rejected because it doesn't scale and rules get forgotten.

## AD-002: Hook-based automation over manual workflows

**Status:** Accepted
**Date:** 2026-02-14

Use SessionStart for orientation, UserPromptSubmit for context surfacing, PreToolUse for guard rails. Hooks automate repetitive tasks so you don't have to remember them. The agent lifecycle has predictable events — use them.

**Alternative considered:** Manual workflow reminders in CLAUDE.md. Rejected because humans forget to follow checklists.

## AD-003: MCP for search, hooks for automation

**Status:** Accepted
**Date:** 2026-02-14

Different tools for different jobs. MCP tools are called by the agent when it needs them (search, read, write). Hooks fire automatically at lifecycle events (load context, save state, block actions). Don't use MCP for things that should always happen, and don't use hooks for things the agent should decide to do.

**Alternative considered:** Everything via MCP. Rejected because MCP requires agent initiative — automation shouldn't depend on the agent remembering to call a tool.

## AD-004: Batch prompts over rapid-fire

**Status:** Accepted
**Date:** 2026-02-14

Fewer, more detailed prompts save tokens and get better results. Each prompt has overhead (system prompt, context reload). A single detailed prompt with all context outperforms five quick follow-ups. Include what, where, why, and constraints.

**Alternative considered:** Conversational back-and-forth style. Still works, but costs more and often produces inferior results for coding tasks.
