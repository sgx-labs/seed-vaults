---
title: "Rate Limiting Strategies"
tags: [rate-limiting, throttling, sliding-window, token-bucket, fixed-window, api-protection]
content_type: hub
domain: api-design
---

# Rate Limiting Strategies

## Overview

Rate limiting protects your API from abuse, ensures fair usage, and prevents a single client from degrading service for everyone. The core question: how many requests should a client be allowed in a given time window?

## Algorithms

### Fixed Window

Count requests in fixed time intervals (e.g., 100 requests per minute, resetting at :00, :01, :02...).

```
Window: 12:00:00 - 12:00:59 -> counter: 0
Request at 12:00:15 -> counter: 1 (allow)
Request at 12:00:45 -> counter: 99 (allow)
Request at 12:00:46 -> counter: 100 (allow)
Request at 12:00:47 -> counter: 101 (deny: 429)
Window resets at 12:01:00 -> counter: 0
```

**Pros**: Simple to implement, low memory. **Cons**: Burst at window boundaries (200 requests in 2 seconds if 100 arrive at :59 and 100 at :00).

### Sliding Window Log

Track timestamps of all requests. Count requests within the sliding window.

```
Window: 60 seconds, limit: 100
Request at T -> count requests where timestamp > (T - 60s)
  If count < 100 -> allow, store timestamp
  If count >= 100 -> deny: 429
```

**Pros**: No boundary burst problem. **Cons**: Memory-intensive (stores every timestamp).

### Sliding Window Counter

Approximation of sliding window using weighted counters from current and previous fixed windows.

```
Previous window count: 80 (weight: 25% of window elapsed = 75%)
Current window count: 30
Weighted count: 80 * 0.75 + 30 = 90 (under 100 limit -> allow)
```

**Pros**: Low memory, smooth limiting. **Cons**: Approximate, not exact.

### Token Bucket

Tokens accumulate at a fixed rate. Each request consumes a token. Allows bursts up to bucket capacity.

```
Bucket capacity: 100
Refill rate: 10 tokens/second
Request -> take 1 token
  If tokens > 0 -> allow
  If tokens == 0 -> deny: 429
```

**Pros**: Allows controlled bursts, smooth long-term rate. **Cons**: Slightly more complex to implement.

## What to Limit By

| Dimension | When | Example |
|-----------|------|---------|
| API key | Per-customer limits | Free: 100/min, Pro: 1000/min |
| IP address | Unauthenticated endpoints | Login: 10/min per IP |
| User ID | Per-user fairness | 500 requests/hour per user |
| Endpoint | Protect expensive operations | Search: 20/min, list: 100/min |
| Global | Protect backend capacity | 10,000 total requests/second |

Combine dimensions: "100 requests per minute per API key, AND 10 requests per second per endpoint per API key."

## Response Headers

Always tell clients their rate limit status:

```
HTTP/1.1 200 OK
X-RateLimit-Limit: 100
X-RateLimit-Remaining: 57
X-RateLimit-Reset: 1620000060

HTTP/1.1 429 Too Many Requests
Retry-After: 30
X-RateLimit-Limit: 100
X-RateLimit-Remaining: 0
X-RateLimit-Reset: 1620000060
```

See `entities/rate-limit-headers.md` for header conventions.

## When to Use What

| Scenario | Algorithm | Why |
|----------|----------|-----|
| Simple API, low traffic | Fixed window | Easy to implement, good enough |
| Public API, fair billing | Sliding window counter | Smooth limits, low overhead |
| High-traffic, burst-tolerant | Token bucket | Handles spikes gracefully |
| Strict compliance (financial) | Sliding window log | Exact counts, no approximation |

## When NOT to Rate Limit

- Internal service-to-service calls in a trusted network (use circuit breakers instead)
- Health check endpoints
- Static assets behind a CDN

## Research Notes

- `research/database-rate-limiting.md` -- Implementing rate limiting with database-backed counters

## See Also

- `hub/error-handling.md` -- 429 response format
- `entities/rate-limit-headers.md` -- Standard rate limit headers
- `research/circuit-breaker.md` -- Circuit breakers for downstream protection
