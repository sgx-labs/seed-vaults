---
title: "Search and Discovery"
tags: [search, discovery, semantic, keyword, queries, tips]
content_type: hub
domain: same-getting-started
---

# Search and Discovery

## Two Search Modes

SAME supports two search modes, and they can work together:

### Semantic Search (with embeddings)

When you have an embedding provider configured (Ollama, OpenAI, etc.), SAME converts your query into an embedding and finds notes with similar meaning. This means:

- "auth strategy" finds a note titled "Authentication Decision"
- "how we handle login" finds notes about JWT tokens
- Meaning matters more than exact words

```bash
same search "how do we handle user authentication?"
```

### Keyword Search (no dependencies)

SAME also has full-text search powered by SQLite FTS5. This works without any embedding provider:

- Matches exact words and phrases
- Great for finding specific terms, error messages, or names
- Always available as a fallback

If you don't have Ollama or another embedding provider, SAME uses keyword search automatically. You can add embeddings later with `same reindex`.

## Search Tips

### Be Specific

```bash
# Too broad -- many results
same search "database"

# Better -- narrows the topic
same search "database migration strategy for postgres"
```

### Use Natural Language

Semantic search understands questions:

```bash
same search "why did we choose JWT over sessions?"
same search "what's the deployment process?"
same search "how does the payment flow work?"
```

### Filter by Tags or Domain

Use filtered search to narrow results:

```bash
# Via MCP tool: search_notes_filtered with domain="backend"
# Via MCP tool: search_notes_filtered with tags=["security"]
```

### Search Across Vaults

If you have multiple vaults (project notes, a SeedVault, personal knowledge), search them all at once:

```bash
same search --all "rate limiting best practices"
```

This uses the `search_across_vaults` MCP tool under the hood.

## The `same ask` Command

`same ask` goes beyond search -- it retrieves relevant notes and generates an answer with citations:

```bash
same ask "what did we decide about caching?"
```

Output:
```
Based on your notes, you decided to use Redis for session caching
(decisions/caching-strategy.md) with a 15-minute TTL. The API layer
handles cache invalidation on writes (research/api-design.md).
```

Every claim is backed by a specific note. No hallucination.

## Finding Related Notes

The `find_similar_notes` MCP tool surfaces connections you might not know about:

```bash
# "I'm reading this note about auth -- what else is related?"
# MCP tool: find_similar_notes with path="decisions/auth-strategy.md"
```

This is powerful for discovering patterns across your vault. A note about "rate limiting" might be related to your "API security" note in ways you hadn't considered.

## Next Steps

- Set up your first vault: `hub/your-first-vault.md`
- Understand how MCP tools power search: `research/how-mcp-works.md`
- See a daily workflow that uses search: `guides/daily-workflow.md`
