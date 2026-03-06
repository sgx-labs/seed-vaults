---
title: "Pagination Patterns"
tags: [pagination, cursor, offset, keyset, page, api-design, performance]
content_type: hub
domain: api-design
---

# Pagination Patterns

## Overview

Any API that returns collections needs pagination. Without it, a single query can return millions of rows, killing your database and your client. The three main approaches are offset-based, cursor-based, and keyset pagination.

## Offset-Based Pagination

The simplest approach. Client specifies `offset` and `limit`.

```
GET /users?offset=20&limit=10
```

Response:
```json
{
  "data": [...],
  "total": 1524,
  "offset": 20,
  "limit": 10
}
```

**Pros**: Simple to understand, supports "jump to page 5", easy to implement with SQL `OFFSET/LIMIT`.

**Cons**: Performance degrades on large datasets (database still scans skipped rows), inconsistent results when data changes between pages (items shift, causing duplicates or missed rows).

**When to use**: Small datasets (under 10K rows), admin panels where users need page numbers, data that rarely changes.

**When NOT to use**: Large datasets, real-time feeds, data that changes frequently.

## Cursor-Based Pagination

Client receives an opaque cursor pointing to the last item. Send it back to get the next page.

```
GET /users?limit=10
GET /users?cursor=eyJpZCI6MjB9&limit=10
```

Response:
```json
{
  "data": [...],
  "cursors": {
    "next": "eyJpZCI6MzB9",
    "previous": "eyJpZCI6MjF9"
  },
  "has_more": true
}
```

The cursor is typically a base64-encoded JSON object containing the sort key(s) of the last item.

**Pros**: Consistent performance regardless of dataset size, stable results even when data changes, works well with real-time feeds.

**Cons**: No "jump to page N", clients must traverse sequentially, slightly more complex to implement.

**When to use**: Large datasets, feeds and timelines, public APIs, any collection that grows or changes frequently.

## Keyset Pagination

Like cursor-based but with transparent parameters instead of opaque cursors. The client sends the sort key values directly.

```
GET /users?after_id=20&limit=10
GET /users?created_after=2024-01-15T10:30:00Z&limit=10
```

SQL:
```sql
SELECT * FROM users
WHERE id > 20
ORDER BY id ASC
LIMIT 10
```

**Pros**: Same performance benefits as cursor-based, transparent parameters (easier to debug), can be bookmarked.

**Cons**: Exposes sort implementation to clients, harder to change sort order later, doesn't work well with multi-column sorts.

**When to use**: Internal APIs where you control both ends, simple sort orders, when cursor opacity is unnecessary.

## Comparison

| Factor | Offset | Cursor | Keyset |
|--------|--------|--------|--------|
| Performance at scale | Poor | Good | Good |
| Jump to page N | Yes | No | No |
| Stable under changes | No | Yes | Yes |
| Implementation complexity | Low | Medium | Low-Medium |
| Client complexity | Low | Low | Medium |
| Multi-column sort | Easy | Medium | Hard |

## Implementation Tips

1. **Always set a maximum page size.** Don't let clients request `limit=1000000`.
2. **Default page size should be reasonable.** 20-50 items is typical.
3. **Include total count only when needed.** `COUNT(*)` is expensive on large tables. Make it optional: `GET /users?limit=10&include_total=true`.
4. **Use the same pagination style across your entire API.** Mixing offset and cursor pagination confuses consumers.
5. **Return pagination metadata in a consistent location.** Top-level object or Link headers -- pick one.

## Research Notes

- `guides/implementing-cursor-pagination.md` -- Step-by-step implementation guide

## See Also

- `hub/rest-fundamentals.md` -- REST conventions for collection endpoints
- `hub/caching.md` -- Caching paginated responses
