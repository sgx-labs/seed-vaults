---
title: "SAME Mastery — Your Personal Memory Layer"
tags: [hub, same, mcp, claude-code, memory, integration, vault]
content_type: hub
domain: productivity
---

# SAME Mastery — Your Personal Memory Layer

## Overview

SAME (Stateless Agent Memory Engine) is the tool that gives your AI agent persistent memory across sessions. It powers semantic search over your vault and knowledge base, manages session handoffs for continuity, logs decisions via MCP hooks, and surfaces relevant notes automatically through embedding-based retrieval. This hub covers SAME's core features, search optimization techniques, vault structure for maximum recall, and how to use hooks and handoffs so your agent never starts from zero. The longer you use it, the smarter your memory layer becomes.

## How SAME Is Different

Most "second brain" tools are passive. You write notes. You organize notes. You hope you find them later. The work of remembering to use the system falls entirely on you.

SAME is active. Through Claude Code hooks and MCP tools, it:
- **Pushes context to you** — Session start loads your last handoff, pinned notes, and recent decisions without you asking
- **Searches on your behalf** — Every prompt can trigger a vault search that surfaces relevant notes
- **Captures automatically** — Session handoffs preserve state without you building a habit
- **Compounds over time** — Each session deposits knowledge; 50 sessions in, the agent knows your patterns

The critical difference: you do not need discipline to use SAME. You just show up and talk to the agent.

## Core Features

| Feature | What It Does | When to Use It |
|---------|-------------|----------------|
| **Session Handoffs** | Records what happened, what is pending, blockers | End of every session |
| **Session Context** | Loads last handoff, pins, recent decisions at session start | Automatic via hooks |
| **Search** | Semantic + keyword search across your vault | When you need to find anything |
| **Decision Logging** | Captures decisions with rationale and rejected alternatives | Any time you make a non-trivial choice |
| **Pinned Notes** | Notes that surface every session until unpinned | Active priorities, reminders, commitments |
| **Cross-Vault Search** | Searches multiple vaults simultaneously | When context spans projects |
| **Session Recovery** | Detects crashed sessions and recovers context | Automatic when sessions end abruptly |

## The Session Lifecycle

```
Session Start
  -> get_session_context loads: last handoff, pinned notes, recent decisions
  -> Agent knows where you left off

During Session
  -> search_notes finds relevant vault content
  -> save_decision captures choices with rationale
  -> save_note creates or updates notes

Session End
  -> create_handoff records summary, pending items, blockers
  -> Next session picks up exactly where this one stopped
```

## Quick Reference: MCP Tools

| Tool | Purpose |
|------|---------|
| `get_session_context` | Load orientation context at session start |
| `create_handoff` | Save session summary for continuity |
| `search_notes` | Semantic search across current vault |
| `search_notes_filtered` | Search with domain/workstream/tag filters |
| `search_across_vaults` | Federated search across multiple vaults |
| `save_note` | Create or update a markdown note |
| `save_decision` | Log a decision with rationale |
| `get_note` | Read a specific note by path |
| `find_similar_notes` | Discover related notes by similarity |
| `recent_activity` | See recently modified notes |
| `reindex` | Re-scan vault after manual changes |
| `index_stats` | Check vault index health |

## The Compound Effect

SAME gets more valuable the more you use it. This is not marketing — it is how semantic search works:
- **Session 1**: The agent knows nothing. You provide all context.
- **Session 10**: Handoffs create a thread. The agent remembers your recent work.
- **Session 50**: Decisions, patterns, and project history are searchable. The agent can say "you faced this before."
- **Session 200**: The vault is a comprehensive knowledge base. Questions that would take you 30 minutes to research take the agent 30 seconds.

Every session is a deposit. The returns compound.

## SAME Integration Research

| Note | Topic |
|------|-------|
| `research/same-integration/same-as-executive-function.md` | How each SAME feature maps to a cognitive function |
| `research/same-integration/body-double-agent.md` | The agent as accountability partner and body double |
| `research/same-integration/context-surfacing-patterns.md` | Optimizing what gets surfaced and when |
| `research/same-integration/decision-memory.md` | Why decisions stay decided with SAME |
| `research/same-integration/vault-as-second-brain.md` | Why passive tools fail and active memory works |
| `research/same-integration/proactive-agent-patterns.md` | Making the agent anticipate your needs |
| `research/same-integration/session-lifecycle-mastery.md` | Getting the most from handoffs, hooks, and recovery |
| `research/same-integration/multi-vault-workflows.md` | Cross-vault search and vault-of-vaults patterns |

## Vault Mastery Research

| Note | Topic |
|------|-------|
| `research/vault-mastery/note-structure-for-retrieval.md` | Writing notes that SAME finds reliably |
| `research/vault-mastery/frontmatter-conventions.md` | Tags, domains, workstreams, and content types |
| `research/vault-mastery/hub-and-spoke-architecture.md` | Hub notes as indexes, research notes as depth |
| `research/vault-mastery/entity-files-that-work.md` | Maintaining entity files the agent actually uses |
| `research/vault-mastery/decision-log-patterns.md` | Structuring decisions for long-term retrieval |
| `research/vault-mastery/vault-hygiene.md` | Pruning, archiving, and keeping the vault healthy |
| `research/vault-mastery/bootstrap-and-onboarding.md` | Setting up a new vault from a seed template |

## Vault Structure Principles

1. **Hub notes are indexes** — They point to detail, they do not duplicate it
2. **Research notes are depth** — One topic, fully explored, with cross-references
3. **Entity files are state** — Who, what, where — kept current by the agent
4. **Decision logs are memory** — What was decided, why, and what was rejected
5. **Templates are scaffolding** — Reduce activation energy for common tasks
6. **Frontmatter enables filtering** — Tags, domains, and workstreams make search precise

## Related Hub Notes

- `hub/adhd.md` — SAME as cognitive accommodation for ADHD brains
- `hub/systems.md` — SAME is the system underneath your system
- `hub/decisions.md` — Decision logging is one of SAME's highest-value features
- `hub/reviews.md` — Reviews are where you verify the system is working
- `hub/tools.md` — Claude Code, SAME, and the tools that connect them
