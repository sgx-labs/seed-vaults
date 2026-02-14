---
title: "Memory — Persistent Memory and Context Management"
tags: [memory, context, RAG, persistent-memory, knowledge-base, context-window]
content_type: hub
domain: engineering
---

# Memory — Persistent Memory and Context Management

## The Memory Problem

LLMs have no memory between sessions. Every conversation starts from zero. For agents that work on ongoing projects, this is the fundamental limitation. You either solve memory or your agent re-discovers everything every session.

## Memory Taxonomy

```
Memory Type        Duration         Mechanism
─────────────────────────────────────────────
Working memory     Within turn      Context window tokens
Short-term         Within session   Conversation history
Long-term          Across sessions  External storage (DB, files)
Episodic           Specific events  Session logs, transcripts
Semantic           Facts/knowledge  Embeddings, knowledge bases
Procedural         How-to skills    System prompts, tools
```

## Context Management Strategies

The context window is the bottleneck. Everything the agent knows must fit in tokens.

| Strategy | How it works | Tradeoff |
|----------|-------------|----------|
| RAG | Retrieve relevant chunks at query time | Quality depends on retrieval accuracy |
| Summarization | Compress conversation history | Lossy — details get dropped |
| Sliding window | Keep last N messages, drop oldest | Loses early context |
| Compaction | AI summarizes then continues with summary | Costs extra inference |
| Structured notes | Agent writes structured notes for future sessions | Requires write-side discipline |

## The Write Side Is Harder

Most memory systems focus on retrieval (read). But the hard problem is **what to write**. An agent that saves everything creates noise. An agent that saves nothing forgets everything.

**Effective write strategies:**
- Save decisions and their rationale (not the deliberation)
- Save outcomes of actions (what worked, what failed)
- Save session handoffs (what was done, what is pending)
- Do NOT save raw conversation transcripts (too noisy, too large)

## Local-First vs Cloud Memory

| Approach | Pros | Cons |
|----------|------|------|
| Local (SQLite, files) | Private, fast, works offline, no API costs | Single machine, manual sync |
| Cloud (Pinecone, Weaviate) | Shared, scalable, managed | Latency, cost, privacy concerns |
| Hybrid | Best of both | More complexity |

For most agent builders, **local-first is the right default**. You can always add cloud sync later. You cannot retroactively make cloud data private.

## Research Notes

- `research/memory/persistent-memory-patterns.md` — How to give agents long-term memory
- `research/memory/context-window-management.md` — Strategies for staying within token limits
- `research/memory/rag-for-agents.md` — RAG patterns specific to agent workloads
- `research/memory/session-handoffs.md` — Carrying state between sessions
- `research/memory/knowledge-base-design.md` — Designing knowledge bases agents can search
