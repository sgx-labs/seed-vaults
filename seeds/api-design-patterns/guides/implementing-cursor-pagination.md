---
title: "Implementing Cursor-Based Pagination"
tags: [guide, pagination, cursor, implementation, database, performance]
content_type: guide
domain: api-design
---

# Implementing Cursor-Based Pagination

## Overview

Cursor-based pagination uses an opaque pointer to the last item in a page. The client sends the cursor back to get the next page. This guide walks through implementation from API design to database query.

## Step 1: Define the API Contract

```
Request:
  GET /items?limit=20              # First page
  GET /items?limit=20&cursor=abc   # Next page

Response:
{
  "data": [...],
  "pagination": {
    "next_cursor": "eyJpZCI6IjEwMCJ9",
    "has_more": true
  }
}
```

Rules:
- `limit` has a default (20) and a maximum (100)
- `cursor` is optional (omit for first page)
- `next_cursor` is null when there are no more results
- `has_more` is a boolean convenience field

## Step 2: Build the Cursor

A cursor encodes the sort key(s) of the last item. Base64-encode a JSON object:

```
Last item: {id: "item_100", created_at: "2025-01-15T10:30:00Z"}

Cursor = base64encode({"id": "item_100", "created_at": "2025-01-15T10:30:00Z"})
       = "eyJpZCI6Iml0ZW1fMTAwIiwiY3JlYXRlZF9hdCI6IjIwMjUtMDEtMTVUMTA6MzA6MDBaIn0="
```

Why encode? Clients treat it as opaque. You can change the internal format without breaking clients.

## Step 3: Database Query

### Simple (Single Sort Key: ID)

```sql
-- First page
SELECT * FROM items
ORDER BY id ASC
LIMIT 21;  -- Fetch limit + 1 to check if there's a next page

-- Next page (cursor decoded: {"id": "item_100"})
SELECT * FROM items
WHERE id > 'item_100'
ORDER BY id ASC
LIMIT 21;
```

If you get 21 rows, there's a next page. Return only the first 20.

### Multi-Column Sort (created_at + id)

When sorting by a non-unique column, you need a tiebreaker (usually ID):

```sql
-- First page
SELECT * FROM items
ORDER BY created_at DESC, id DESC
LIMIT 21;

-- Next page (cursor: {"created_at": "2025-01-15T10:30:00Z", "id": "item_100"})
SELECT * FROM items
WHERE (created_at, id) < ('2025-01-15T10:30:00Z', 'item_100')
ORDER BY created_at DESC, id DESC
LIMIT 21;
```

The tuple comparison `(created_at, id) < (value, value)` handles the tiebreaker correctly.

**Index**: Create a composite index matching your sort order:
```sql
CREATE INDEX idx_items_created_id ON items (created_at DESC, id DESC);
```

## Step 4: Handler Logic

```
function listItems(request):
  limit = min(request.query.limit or 20, 100)  // Cap at 100
  cursor = decodeCursor(request.query.cursor)    // null for first page

  // Fetch limit + 1 to detect next page
  items = db.query(buildQuery(cursor, limit + 1))

  hasMore = items.length > limit
  if hasMore:
    items = items[0:limit]  // Remove the extra item

  nextCursor = null
  if hasMore:
    lastItem = items[items.length - 1]
    nextCursor = encodeCursor(lastItem)

  return {
    data: items,
    pagination: {
      next_cursor: nextCursor,
      has_more: hasMore
    }
  }

function encodeCursor(item):
  return base64encode(json({id: item.id, created_at: item.created_at}))

function decodeCursor(cursorString):
  if cursorString is null:
    return null
  return jsonParse(base64decode(cursorString))
```

## Step 5: Validation and Edge Cases

- **Invalid cursor**: Return 400 with clear error message
- **Cursor for deleted item**: The WHERE clause still works (it finds items after the position)
- **Empty results**: Return empty data array with `has_more: false`
- **Limit of 0**: Return 400 (or treat as "use default")
- **Negative limit**: Return 400

## Performance Notes

- Cursor pagination uses a WHERE clause, not OFFSET -- constant performance regardless of page depth
- Ensure your sort columns are indexed
- The "fetch N+1" trick avoids a separate COUNT query

## See Also

- `hub/pagination.md` -- Comparison of pagination strategies
- `hub/rest-fundamentals.md` -- REST collection endpoints
