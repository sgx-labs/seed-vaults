---
title: "How Do I Use RAG Effectively for Agent Workloads?"
tags: [RAG, retrieval, embeddings, vector-search, agents, reranking, hybrid-search, metadata-filters]
content_type: research
domain: engineering
---

# How Do I Use RAG Effectively for Agent Workloads?

## The Question

How is RAG for agents different from RAG for chatbots, and what patterns work best?

## Agent RAG vs Chatbot RAG

| Aspect | Chatbot RAG | Agent RAG |
|--------|------------|-----------|
| Query source | User question | Agent's current task/context |
| Timing | Per user message | Per decision point (may be multiple per turn) |
| Scope | Answer the question | Inform the next action |
| Freshness | Static knowledge base | May need current session context |
| Volume | 1 retrieval per turn | Multiple retrievals per complex task |

## Agent-Specific RAG Patterns

### 1. Task-Aware Retrieval

Do not just search for what the user said. Search for what the agent needs to complete its current task.

```python
# Bad: Search the user's raw question
results = search("how do I fix the auth bug?")

# Good: Search for task-relevant context
results = search("authentication middleware error handling patterns")
results += search("recent auth module changes and decisions")
```

### 2. Multi-Query Retrieval

For complex tasks, issue multiple retrieval queries to cover different aspects:

```python
queries = [
    "database schema for user preferences",     # structural
    "decisions about preference storage format",  # decisions
    "recent changes to user settings module",     # history
]
results = merge_and_deduplicate([search(q) for q in queries])
```

### 3. Reranking

Embedding similarity is a rough signal. Reranking with a cross-encoder or LLM improves precision:

```python
candidates = vector_search(query, top_k=20)  # broad retrieval
reranked = reranker.score(query, candidates)  # precise reranking
top_results = sorted(reranked, key=lambda x: x.score, reverse=True)[:5]
```

### 4. Retrieval with Metadata Filters

Combine semantic search with structured filters:

```python
results = search(
    query="authentication patterns",
    filters={"domain": "engineering", "type": "decision"},
    top_k=5,
)
```

This prevents the agent from getting irrelevant results from other domains.

## RAG Pipeline for Agents

```
Agent decides it needs context
  → Formulate query (may be multi-query)
  → Retrieve candidates (vector + keyword hybrid)
  → Rerank for relevance
  → Filter by metadata/recency
  → Inject top results into agent context
  → Agent reasons with augmented context
```

## Common RAG Mistakes for Agents

1. **Retrieving too much.** Agents get confused by irrelevant context. Better to return 3 highly relevant results than 10 mediocre ones.
2. **No hybrid search.** Vector search alone misses exact keyword matches. Combine with full-text search (BM25/FTS5).
3. **Stale embeddings.** If the knowledge base changes but embeddings are not updated, retrieval returns outdated information.
4. **No chunk context.** Returning isolated chunks without surrounding context. Include the note title and section header.

## See Also

- `hub/memory.md` — Memory overview
- `research/memory/knowledge-base-design.md` — Knowledge base design
- `research/memory/persistent-memory-patterns.md` — Memory implementation
