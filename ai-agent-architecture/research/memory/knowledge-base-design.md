---
title: "How Do I Design a Knowledge Base That Agents Can Search?"
tags: [knowledge-base, design, search, embeddings, organization, frontmatter, chunking, retrieval-quality]
content_type: research
domain: engineering
---

# How Do I Design a Knowledge Base That Agents Can Search?

## The Question

How do you structure a knowledge base so that agent retrieval actually returns useful results?

## Design Principles

### 1. One Concept Per Document

Do not put everything about a project in one giant file. Split by concept, question, or decision. This makes retrieval precise — the agent gets exactly the note it needs, not a 500-line file where 10 lines are relevant.

```
Bad:  docs/project-notes.md (everything in one file)
Good: research/auth/jwt-vs-sessions.md (one decision)
      research/auth/rate-limiting-approach.md (one topic)
      research/database/migration-strategy.md (one topic)
```

### 2. Descriptive Titles

The title is the most important retrieval signal. It should be a natural language question or topic that someone would search for.

```
Bad:  notes.md, misc.md, stuff.md
Good: "When Should I Use JWT vs Session Cookies?"
      "How Does the Rate Limiting Middleware Work?"
      "Database Migration Strategy for v2"
```

### 3. Frontmatter for Filtering

Add structured metadata that enables filtered search:

```yaml
---
title: "JWT vs Session Authentication"
tags: [auth, jwt, sessions, security]
content_type: research
domain: engineering
---
```

Tags enable filtered retrieval: "search for auth decisions" returns only notes tagged with `auth`.

### 4. Cross-References

Link related notes so the agent can follow threads:

```markdown
## See Also
- `research/auth/rate-limiting.md` — Rate limiting for auth endpoints
- `decisions/auth-approach.md` — Why we chose JWT
```

### 5. Keep Notes Under 200 Lines

Long notes degrade retrieval quality (the relevant section gets diluted by surrounding text) and waste context window tokens when injected. If a note exceeds 200 lines, split it.

## Knowledge Base Architecture

```
knowledge-base/
├── hub/                  # Category overviews (entry points)
├── research/             # Detailed guides (one question per file)
│   ├── architecture/
│   ├── security/
│   └── deployment/
├── entities/             # Living docs (things that change)
├── decisions/            # Architectural decisions with rationale
└── sessions/             # Session handoffs
```

This structure maps to how agents search:
- "What do we know about X?" → Search research/
- "What did we decide about Y?" → Search decisions/
- "What is the current state of Z?" → Read entities/
- "What happened last session?" → Read sessions/

## Embedding Considerations

- **Chunk size:** 200-500 tokens per chunk works well for most embedding models
- **Overlap:** 50-100 token overlap between chunks preserves context across boundaries
- **Metadata:** Store title, tags, and file path as metadata alongside embeddings
- **Freshness:** Re-embed when notes change. Stale embeddings return stale results.

## See Also

- `hub/memory.md` — Memory overview
- `research/memory/rag-for-agents.md` — RAG patterns for agents
- `research/memory/persistent-memory-patterns.md` — Memory implementation
