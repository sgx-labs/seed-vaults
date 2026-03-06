---
title: "Rate Limit Headers (X-RateLimit-*)"
tags: [rate-limiting, headers, x-ratelimit, retry-after, reference, entity]
content_type: entity
domain: api-design
---

# Rate Limit Headers (X-RateLimit-*)

## Standard Headers

There is no official RFC for rate limit headers yet (draft-ietf-httpapi-ratelimit-headers is in progress), but these conventions are widely adopted:

| Header | Purpose | Example |
|--------|---------|---------|
| `X-RateLimit-Limit` | Maximum requests allowed in the window | `100` |
| `X-RateLimit-Remaining` | Requests remaining in the current window | `57` |
| `X-RateLimit-Reset` | When the window resets (Unix timestamp) | `1705312260` |
| `Retry-After` | Seconds to wait before retrying (on 429) | `30` |

## Draft Standard Headers (IETF)

The IETF draft uses unprefixed names:

| Header | Purpose |
|--------|---------|
| `RateLimit-Limit` | Same as X-RateLimit-Limit |
| `RateLimit-Remaining` | Same as X-RateLimit-Remaining |
| `RateLimit-Reset` | Seconds until reset (not Unix timestamp) |

Note the difference: the draft uses seconds-until-reset, not a Unix timestamp.

## Example Responses

### Normal Request (Within Limits)
```
HTTP/1.1 200 OK
X-RateLimit-Limit: 100
X-RateLimit-Remaining: 57
X-RateLimit-Reset: 1705312260
Content-Type: application/json

{"data": [...]}
```

### Rate Limited (429)
```
HTTP/1.1 429 Too Many Requests
X-RateLimit-Limit: 100
X-RateLimit-Remaining: 0
X-RateLimit-Reset: 1705312260
Retry-After: 30
Content-Type: application/json

{
  "type": "https://api.example.com/errors/rate-limited",
  "title": "Rate Limit Exceeded",
  "status": 429,
  "detail": "You have exceeded 100 requests per minute. Try again in 30 seconds."
}
```

## Multi-Tier Rate Limits

Some APIs have multiple limits (per-second and per-day):

```
X-RateLimit-Limit: 100
X-RateLimit-Remaining: 57
X-RateLimit-Reset: 1705312260
X-RateLimit-Limit-Day: 10000
X-RateLimit-Remaining-Day: 8432
```

Or use a structured format:
```
RateLimit: limit=100, remaining=57, reset=30
RateLimit: limit=10000, remaining=8432, reset=43200; window=86400
```

## Implementation Notes

- **Always include rate limit headers** on successful responses too, not just 429s
- **Use Unix timestamps** for Reset (most common) or seconds-until-reset (IETF draft)
- **Be consistent** -- pick one format across your entire API
- **Include Retry-After on 429** -- this is the one clients actually use for backoff
- **Include Retry-After on 503** -- also useful when service is temporarily unavailable

## See Also

- `hub/rate-limiting.md` -- Rate limiting algorithms and strategies
- `research/database-rate-limiting.md` -- Implementation approaches
- `entities/security-headers.md` -- Other API headers
