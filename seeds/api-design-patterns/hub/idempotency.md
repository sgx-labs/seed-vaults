---
title: "Idempotency"
tags: [idempotency, idempotent, retry, safety, duplicate, idempotency-key]
content_type: hub
domain: api-design
---

# Idempotency

## Overview

An operation is idempotent if performing it multiple times produces the same result as performing it once. This is critical for API reliability -- network failures, timeouts, and retries mean clients will inevitably send duplicate requests. Your API must handle this gracefully.

## HTTP Method Idempotency

| Method | Idempotent? | Why |
|--------|------------|-----|
| GET | Yes | Reading doesn't change state |
| PUT | Yes | Replacing a resource with the same data = same result |
| DELETE | Yes | Deleting something twice = still deleted |
| HEAD | Yes | Same as GET, no body |
| OPTIONS | Yes | Metadata query |
| POST | No | Creating a resource twice = two resources |
| PATCH | Not guaranteed | Depends on the patch operation |

The problem: POST is the most common mutation method, and it's not idempotent.

## Idempotency Keys

The standard solution for non-idempotent operations. The client sends a unique key with each request. If the server sees the same key again, it returns the cached response instead of processing again.

```
POST /payments HTTP/1.1
Idempotency-Key: 7a3b4c5d-1234-5678-9abc-def012345678
Content-Type: application/json

{"amount": 1000, "currency": "USD"}
```

Server behavior:
```
1. Receive request with Idempotency-Key
2. Check if key exists in store
   a. Key not found -> process request, store result with key, return response
   b. Key found, same request body -> return cached response
   c. Key found, different request body -> return 422 (key reuse with different params)
3. Key expires after TTL (typically 24-48 hours)
```

**Implementation notes**:
- Store keys in Redis or a database with TTL
- Store the full response (status code, headers, body)
- Use a unique constraint to prevent race conditions
- Lock the key during processing to prevent concurrent duplicates

## Natural Idempotency

Some operations are naturally idempotent without extra keys:

```
PUT /users/123 {"name": "Alice"}       # Naturally idempotent
DELETE /users/123                        # Naturally idempotent
PATCH /users/123 {"status": "active"}   # Idempotent (set to value)
PATCH /users/123 {"balance": "+10"}     # NOT idempotent (increment)
```

Design operations as "set to X" instead of "increment by Y" when possible.

## When to Use Idempotency Keys

**Always use for**: Payment processing, order creation, any operation with real-world side effects (sending emails, charging cards, creating resources).

**Optional for**: Non-critical operations where duplicates are harmless or easily detected.

**Don't need for**: GET, PUT, DELETE (already idempotent), operations that are naturally deduplicatable (e.g., "set user status to active").

## Common Patterns

### Stripe's Approach
```
POST /v1/charges
Idempotency-Key: unique-string-per-request
```
Keys expire after 24 hours. Same key + same parameters = cached response. Same key + different parameters = error.

### Database-Level Deduplication
```sql
INSERT INTO orders (id, idempotency_key, ...)
  VALUES (gen_id(), 'abc123', ...)
  ON CONFLICT (idempotency_key) DO NOTHING
  RETURNING *;
```

### Client-Side Best Practices
- Generate a UUID for each unique operation (not each retry)
- Store the key locally until the operation succeeds
- On timeout or network error, retry with the SAME key

## See Also

- `hub/error-handling.md` -- Error responses for idempotency violations
- `research/retry-strategies.md` -- Retry patterns that rely on idempotency
- `hub/rest-fundamentals.md` -- HTTP method safety and idempotency
