---
title: "Batch Operations Design"
tags: [batch, bulk, operations, performance, api-design, transactions]
content_type: research
domain: api-design
---

# Batch Operations Design

## The Core Idea

Sometimes clients need to create, update, or delete many resources at once. Individual API calls for each item are slow (network round trips) and wasteful (connection overhead). Batch operations let clients send multiple operations in a single request.

## Approach 1: Array of Items

Send an array in the request body. All items processed together.

```
POST /api/users/batch
Content-Type: application/json

[
  {"name": "Alice", "email": "alice@test.com"},
  {"name": "Bob", "email": "bob@test.com"},
  {"name": "Charlie", "email": "charlie@test.com"}
]
```

Response (all succeed):
```json
{
  "created": 3,
  "results": [
    {"id": "usr_1", "name": "Alice", "status": "created"},
    {"id": "usr_2", "name": "Bob", "status": "created"},
    {"id": "usr_3", "name": "Charlie", "status": "created"}
  ]
}
```

Response (partial failure):
```json
{
  "created": 2,
  "failed": 1,
  "results": [
    {"id": "usr_1", "name": "Alice", "status": "created"},
    {"name": "Bob", "status": "failed", "error": {"code": "DUPLICATE_EMAIL", "message": "Email already exists"}},
    {"id": "usr_3", "name": "Charlie", "status": "created"}
  ]
}
```

## Approach 2: Mixed Operations

Batch different operation types in a single request. Used by Google and Microsoft APIs.

```
POST /api/batch
Content-Type: application/json

{
  "operations": [
    {"method": "POST", "path": "/users", "body": {"name": "Alice"}},
    {"method": "PATCH", "path": "/users/123", "body": {"name": "Bob Updated"}},
    {"method": "DELETE", "path": "/users/456"}
  ]
}
```

Response:
```json
{
  "results": [
    {"status": 201, "body": {"id": "usr_1", "name": "Alice"}},
    {"status": 200, "body": {"id": "123", "name": "Bob Updated"}},
    {"status": 204, "body": null}
  ]
}
```

## Approach 3: Async Batch (Job-Based)

For large batches, accept the request and process asynchronously.

```
POST /api/users/import
Content-Type: application/json

{"items": [...1000 users...]}

Response:
  202 Accepted
  {
    "job_id": "job_abc123",
    "status": "processing",
    "status_url": "/api/jobs/job_abc123"
  }
```

Client polls for completion:
```
GET /api/jobs/job_abc123

{
  "job_id": "job_abc123",
  "status": "completed",
  "total": 1000,
  "succeeded": 987,
  "failed": 13,
  "errors_url": "/api/jobs/job_abc123/errors"
}
```

## Design Decisions

### All-or-Nothing vs Partial Success

**All-or-nothing (transactional)**: If any item fails, roll back everything. Return 400/422.
- Use for: Financial transactions, operations that must be consistent.
- Return: Single success or single failure.

**Partial success**: Process each item independently. Report individual results.
- Use for: Bulk imports, non-critical updates, idempotent operations.
- Return: 200 with per-item status (always 200 even if some items failed).

**Recommendation**: Partial success for most batch operations. All-or-nothing only when consistency requires it.

### HTTP Status Code for Partial Failure

The batch request succeeded (it was processed), but some items failed:
- **200 OK** with a response body showing per-item results (most common)
- **207 Multi-Status** (WebDAV, less common but semantically correct)

Don't use 400 or 500 for partial failures -- the request itself was valid.

### Limits

Always set limits:
- Maximum items per batch (50-1000 depending on operation complexity)
- Maximum request body size
- Rate limiting per batch (count as N requests, not 1)

## Common Mistakes

- **No item limit** -- a client sends 1 million items and your server crashes.
- **No per-item error reporting** -- client can't tell which items failed.
- **Batch counts as one request for rate limiting** -- a batch of 1000 should count as 1000.
- **No async option for large batches** -- synchronous processing of 10K items times out.
- **Inconsistent response format** -- success and failure should use the same structure.

## See Also

- `hub/error-handling.md` -- Error format for batch item failures
- `hub/idempotency.md` -- Making batch operations retryable
- `hub/rate-limiting.md` -- Rate limiting batch operations
