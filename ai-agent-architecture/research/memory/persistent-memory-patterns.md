---
title: "How Do I Give Agents Persistent Memory Across Sessions?"
tags: [memory, persistent, cross-session, knowledge-base, embeddings, SQLite, vector-db, SAME]
content_type: research
domain: engineering
---

# How Do I Give Agents Persistent Memory Across Sessions?

## The Question

How do you make an agent remember things from previous sessions without stuffing everything into the context window?

## The Fundamental Problem

LLMs are stateless. Every API call starts fresh. The model has no memory of prior conversations unless you explicitly provide context. For agents that work on ongoing projects, this means every session starts from zero unless you solve the memory problem.

## Memory Architecture

```
Agent Session
  ↕ reads at session start
Memory Layer
  ├── Session handoffs (what happened last time)
  ├── Decision log (choices made and why)
  ├── Knowledge base (searchable facts and patterns)
  └── Entity tracker (current state of things that change)
  ↕ writes during/after session
Persistent Storage (SQLite, filesystem, vector DB)
```

## Implementation Approaches

### 1. File-Based Memory (Simplest)

Write markdown files. Read them at session start.

```markdown
# Session Handoff — 2026-02-14

## Completed
- Refactored auth module to use JWT
- Added rate limiting middleware

## Pending
- Database migration for user preferences table
- Integration tests for new auth flow

## Key Decisions
- Chose JWT over session cookies (see decisions/auth-approach.md)
```

**Pros:** Human-readable, version-controllable, zero infrastructure.
**Cons:** No semantic search. Linear scan. Does not scale past ~50 files.

### 2. Embedding-Based Memory (Searchable)

Store notes as vector embeddings. Search by semantic similarity.

```python
# Write
embedding = embed("JWT auth decision: chose JWT over session cookies for...")
db.insert(text=note_text, embedding=embedding, metadata={"type": "decision"})

# Read
query_embedding = embed("how did we handle authentication?")
results = db.search(query_embedding, top_k=5)
```

**Pros:** Semantic search. Scales to thousands of notes. Finds relevant context even with different wording.
**Cons:** Requires embedding model. Retrieval quality depends on embedding quality.

### 3. Structured Memory (Database)

Store facts in a structured database. Query by type, date, project.

```sql
SELECT * FROM decisions
WHERE project = 'api-service'
AND created_at > '2026-01-01'
ORDER BY created_at DESC
LIMIT 5;
```

**Pros:** Precise queries. Relationships between facts. Fast.
**Cons:** Schema must be predefined. Less flexible than free-text.

## The Write-Side Discipline

The hardest part of persistent memory is not retrieval — it is deciding what to write. An agent that saves everything creates a haystack. An agent that saves nothing forgets.

**Write these:**
- Decisions and their rationale
- Session summaries (what was done, what is pending)
- Errors encountered and how they were resolved
- Key findings from research or debugging

**Do NOT write these:**
- Full conversation transcripts (too noisy)
- Intermediate reasoning (only final conclusions matter)
- Raw tool outputs (store summaries instead)

## Tools That Solve This

- **SAME** — SQLite + embeddings, local-first, hook-based injection into Claude Code sessions
- **Mem0** — Cloud-based memory service with entity extraction
- **Letta** — Long-term memory with automatic summarization
- **Custom** — SQLite + any embedding model for full control

## See Also

- `hub/memory.md` — Memory overview
- `research/memory/session-handoffs.md` — Session handoff patterns
- `research/memory/knowledge-base-design.md` — Knowledge base design
