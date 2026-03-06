---
title: "Database-Backed Rate Limiting"
tags: [rate-limiting, database, redis, sliding-window, implementation, counters]
content_type: research
domain: api-design
---

# Database-Backed Rate Limiting

## The Core Idea

Not every API has Redis. Rate limiting can be implemented with your existing database, an in-memory store, or Redis. Each approach has different tradeoffs for accuracy, performance, and operational complexity.

## In-Memory (No External Dependencies)

Store counters in your application's memory.

```
// In-memory map
rateLimits = Map<clientKey, {count: int, windowStart: timestamp}>

function checkRateLimit(clientKey, limit, windowSeconds):
  entry = rateLimits.get(clientKey)
  now = currentTime()

  if entry is null OR (now - entry.windowStart) > windowSeconds:
    rateLimits.set(clientKey, {count: 1, windowStart: now})
    return ALLOW

  if entry.count >= limit:
    return DENY

  entry.count += 1
  return ALLOW
```

**Pros**: Zero dependencies, fast, simple.
**Cons**: Not shared across instances (each server has its own counters), lost on restart, memory grows with unique clients.

**When to use**: Single-instance deployments, development, low-traffic APIs where approximate limiting is acceptable.

## Redis

The industry standard for distributed rate limiting.

### Fixed Window with Redis
```
function checkRateLimit(clientKey, limit, windowSeconds):
  key = "ratelimit:" + clientKey + ":" + floor(now / windowSeconds)

  count = INCR(key)
  if count == 1:
    EXPIRE(key, windowSeconds)

  if count > limit:
    return DENY
  return ALLOW
```

### Sliding Window with Redis
```
function checkRateLimit(clientKey, limit, windowSeconds):
  key = "ratelimit:" + clientKey
  now = currentTimestamp()
  windowStart = now - windowSeconds

  // Remove old entries
  ZREMRANGEBYSCORE(key, 0, windowStart)

  // Count entries in window
  count = ZCARD(key)

  if count >= limit:
    return DENY

  // Add current request
  ZADD(key, now, uniqueRequestId)
  EXPIRE(key, windowSeconds)
  return ALLOW
```

**Pros**: Shared across all instances, survives restarts, fast.
**Cons**: Redis dependency, network latency per request, Redis failure = rate limiting failure.

### Token Bucket with Redis
```
function checkRateLimit(clientKey, capacity, refillRate):
  key = "bucket:" + clientKey
  now = currentTimestamp()

  bucket = GET(key)  // {tokens, lastRefill}

  if bucket is null:
    bucket = {tokens: capacity - 1, lastRefill: now}
    SET(key, bucket, EX=3600)
    return ALLOW

  // Refill tokens
  elapsed = now - bucket.lastRefill
  newTokens = min(capacity, bucket.tokens + elapsed * refillRate)

  if newTokens < 1:
    return DENY

  bucket.tokens = newTokens - 1
  bucket.lastRefill = now
  SET(key, bucket, EX=3600)
  return ALLOW
```

## Database (PostgreSQL/MySQL)

When you don't want Redis but need shared state.

```sql
CREATE TABLE rate_limits (
  client_key VARCHAR(255) NOT NULL,
  window_start TIMESTAMP NOT NULL,
  request_count INTEGER NOT NULL DEFAULT 1,
  PRIMARY KEY (client_key, window_start)
);

-- Check and increment (atomic)
INSERT INTO rate_limits (client_key, window_start, request_count)
VALUES ('user:123', date_trunc('minute', NOW()), 1)
ON CONFLICT (client_key, window_start)
DO UPDATE SET request_count = rate_limits.request_count + 1
RETURNING request_count;

-- If returned count > limit, deny the request
```

**Pros**: No new infrastructure, atomic operations, shared across instances.
**Cons**: Database load per request, slower than Redis, cleanup required for old rows.

**Cleanup**:
```sql
DELETE FROM rate_limits WHERE window_start < NOW() - INTERVAL '1 hour';
```
Run this periodically (cron job or background task).

## Handling Rate Limiter Failures

What happens when Redis is down or the database is slow?

| Strategy | Behavior | Risk |
|----------|----------|------|
| Fail open | Allow all requests | API abuse during outage |
| Fail closed | Deny all requests | Service outage for everyone |
| Degrade to in-memory | Use local counters | Inaccurate but functional |

**Recommendation**: Fail open with alerting for most APIs. Fail closed only for APIs where abuse has severe consequences (payment processing).

## Choosing an Approach

| Factor | In-Memory | Redis | Database |
|--------|-----------|-------|----------|
| Multi-instance | No | Yes | Yes |
| Accuracy | Per-instance | Global | Global |
| Latency | Lowest | Low | Medium |
| Dependencies | None | Redis | Existing DB |
| Operational cost | None | Redis ops | Cleanup jobs |

## See Also

- `hub/rate-limiting.md` -- Rate limiting algorithms and strategies
- `entities/rate-limit-headers.md` -- Response headers for rate limiting
