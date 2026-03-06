---
title: "Search Implementation"
tags: [search, meilisearch, algolia, pg-trgm, full-text-search, fuzzy, postgresql]
content_type: research
domain: typescript-fullstack
---

# Search Implementation

## The Core Idea

Three tiers of search: PostgreSQL built-in (free, good enough for most apps), Meilisearch (self-hosted, great DX), and Algolia (managed, instant). Choose based on dataset size, search quality requirements, and budget.

## Tier 1: PostgreSQL Full-Text Search + pg_trgm

Good for up to ~1M rows. No additional infrastructure.

```sql
-- Enable trigram extension
CREATE EXTENSION IF NOT EXISTS pg_trgm;

-- Add search columns and indexes
ALTER TABLE posts ADD COLUMN search_vector tsvector
  GENERATED ALWAYS AS (
    setweight(to_tsvector('english', coalesce(title, '')), 'A') ||
    setweight(to_tsvector('english', coalesce(content, '')), 'B')
  ) STORED;

CREATE INDEX posts_search_idx ON posts USING GIN (search_vector);
CREATE INDEX posts_title_trgm_idx ON posts USING GIN (title gin_trgm_ops);
```

```typescript
// Prisma raw query for full-text search
async function searchPosts(query: string, limit = 20) {
  return db.$queryRaw`
    SELECT id, title, ts_rank(search_vector, plainto_tsquery('english', ${query})) AS rank
    FROM posts
    WHERE search_vector @@ plainto_tsquery('english', ${query})
    ORDER BY rank DESC
    LIMIT ${limit}
  `;
}

// Fuzzy search with trigrams (handles typos)
async function fuzzySearch(query: string) {
  return db.$queryRaw`
    SELECT id, title, similarity(title, ${query}) AS sim
    FROM posts
    WHERE similarity(title, ${query}) > 0.3
    ORDER BY sim DESC
    LIMIT 20
  `;
}
```

## Tier 2: Meilisearch — Self-Hosted Search Engine

Sub-50ms search with typo tolerance, faceting, and filtering. Open source.

```typescript
// lib/search.ts
import { MeiliSearch } from "meilisearch";

const meilisearch = new MeiliSearch({
  host: process.env.MEILISEARCH_URL!,
  apiKey: process.env.MEILISEARCH_API_KEY!,
});

// Index documents
async function indexPosts() {
  const posts = await db.post.findMany({
    where: { published: true },
    select: { id: true, title: true, content: true, tags: true, authorName: true },
  });

  const index = meilisearch.index("posts");
  await index.updateSettings({
    searchableAttributes: ["title", "content", "tags", "authorName"],
    filterableAttributes: ["tags", "authorName"],
    sortableAttributes: ["createdAt"],
  });

  await index.addDocuments(posts);
}

// Search
export async function searchPosts(query: string, filters?: { tag?: string }) {
  const index = meilisearch.index("posts");
  return index.search(query, {
    limit: 20,
    filter: filters?.tag ? `tags = "${filters.tag}"` : undefined,
    attributesToHighlight: ["title", "content"],
    highlightPreTag: "<mark>",
    highlightPostTag: "</mark>",
  });
}
```

```typescript
// API route
export async function GET(request: Request) {
  const { searchParams } = new URL(request.url);
  const q = searchParams.get("q") ?? "";
  const tag = searchParams.get("tag") ?? undefined;

  const results = await searchPosts(q, { tag });
  return Response.json(results);
}
```

## Tier 3: Algolia — Managed, Instant

Hosted search-as-a-service. Best UX with InstantSearch components.

```typescript
// lib/algolia.ts
import algoliasearch from "algoliasearch";

const client = algoliasearch(
  process.env.ALGOLIA_APP_ID!,
  process.env.ALGOLIA_ADMIN_KEY!
);

const index = client.initIndex("posts");

// Sync on data change
export async function syncPostToAlgolia(post: Post) {
  await index.saveObject({
    objectID: post.id,
    title: post.title,
    content: post.content?.substring(0, 500), // Algolia has record size limits
    tags: post.tags,
  });
}

export async function removePostFromAlgolia(postId: string) {
  await index.deleteObject(postId);
}
```

## Comparison

| Factor | PostgreSQL | Meilisearch | Algolia |
|--------|-----------|-------------|---------|
| Cost | Free | Free (self-hosted) | Paid |
| Typo tolerance | pg_trgm (basic) | Excellent | Excellent |
| Setup | Minutes | 1 hour | 30 min |
| Latency | 10-100ms | 10-50ms | 10-30ms |
| Faceting | Manual | Built-in | Built-in |
| Max dataset | ~1M rows | ~10M docs | Unlimited |
| Infrastructure | None (use your DB) | Docker/hosted | Managed |

## Decision Framework

1. **< 100K rows, basic search?** -> PostgreSQL `LIKE` or `tsvector`
2. **< 1M rows, need fuzzy matching?** -> PostgreSQL with `pg_trgm`
3. **Need facets, typo tolerance, instant results?** -> Meilisearch
4. **Budget for managed, need global distribution?** -> Algolia

## See Also

- `hub/database-patterns.md` — PostgreSQL optimization
- `hub/api-route-patterns.md` — Search API endpoints
- `hub/performance-patterns.md` — Search UX performance
