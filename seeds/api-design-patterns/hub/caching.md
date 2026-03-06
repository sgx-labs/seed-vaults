---
title: "Caching Strategies"
tags: [caching, etag, cache-control, conditional-requests, performance, cdn]
content_type: hub
domain: api-design
---

# Caching Strategies

## Overview

Caching reduces latency, server load, and bandwidth. HTTP has built-in caching mechanisms that most APIs underuse. The two main approaches are expiration-based (Cache-Control) and validation-based (ETags / Last-Modified).

## Cache-Control Headers

Tell clients and intermediaries how long a response is valid.

```
Cache-Control: public, max-age=3600          # CDNs and browsers: cache for 1 hour
Cache-Control: private, max-age=300          # Browser only: cache for 5 minutes
Cache-Control: no-cache                       # Always revalidate before using
Cache-Control: no-store                       # Never cache (sensitive data)
```

| Directive | Meaning |
|-----------|---------|
| `public` | Any cache can store this (CDN, proxy, browser) |
| `private` | Only the end client can cache (user-specific data) |
| `max-age=N` | Fresh for N seconds |
| `s-maxage=N` | Override max-age for shared caches (CDNs) |
| `no-cache` | Cache it, but revalidate every time |
| `no-store` | Don't cache at all |
| `must-revalidate` | Once stale, must revalidate before use |

## ETags (Entity Tags)

A fingerprint of the response content. Enables conditional requests.

```
# First request
GET /users/123
Response:
  ETag: "abc123"
  {"name": "Alice", "email": "alice@test.com"}

# Subsequent request
GET /users/123
If-None-Match: "abc123"

Response (if unchanged):
  304 Not Modified        # No body, saves bandwidth

Response (if changed):
  200 OK
  ETag: "def456"
  {"name": "Alice B.", "email": "alice@test.com"}
```

**Strong ETags**: Byte-for-byte identical (`"abc123"`)
**Weak ETags**: Semantically equivalent (`W/"abc123"`)

## Last-Modified

Timestamp-based validation. Simpler than ETags but less precise.

```
# First request
GET /reports/daily
Response:
  Last-Modified: Wed, 15 Jan 2025 10:30:00 GMT

# Subsequent request
GET /reports/daily
If-Modified-Since: Wed, 15 Jan 2025 10:30:00 GMT

Response (if unchanged):
  304 Not Modified
```

## What to Cache

| Resource Type | Strategy | TTL |
|--------------|----------|-----|
| Static reference data | Cache-Control + long max-age | Hours to days |
| User profiles | ETag + short max-age | Minutes |
| Search results | Cache-Control, short TTL | 30-60 seconds |
| Real-time data | no-cache or no-store | N/A |
| Authenticated responses | private + ETag | Minutes |
| Public list endpoints | public + s-maxage | Minutes to hours |

## Cache Invalidation

The hard part. Options:

1. **Time-based expiry** -- set max-age, accept staleness within the window
2. **Event-based purge** -- when data changes, purge the cache key
3. **Versioned URLs** -- `/v2/users` or `/users?v=abc123` busts the cache
4. **Surrogate keys** -- tag cached responses, purge by tag

**Rule of thumb**: prefer short TTLs + conditional requests over complex invalidation.

## Common Mistakes

- **Not caching at all** -- the default for most APIs. Add Cache-Control headers.
- **Caching authenticated responses as public** -- user A sees user B's data.
- **Not using Vary headers** -- if responses differ by Accept or Authorization, you need `Vary: Accept, Authorization`.
- **Long TTLs without invalidation** -- users see stale data with no way to refresh.
- **Caching error responses** -- a transient 500 cached for an hour is a bad day.

## See Also

- `hub/rest-fundamentals.md` -- HTTP caching is a REST constraint
- `hub/pagination.md` -- Caching paginated responses
- `hub/cors.md` -- CORS and caching interactions
- `entities/content-type-headers.md` -- Content negotiation affects caching
