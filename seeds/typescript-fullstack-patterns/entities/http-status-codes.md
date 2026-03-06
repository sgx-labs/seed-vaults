---
title: "HTTP Status Codes for API Routes"
tags: [http, status-codes, reference, entity, api, nextjs]
content_type: entity
domain: typescript-fullstack
---

# HTTP Status Codes for API Routes

## Success (2xx)

| Code | Name | When to Use | Next.js Example |
|------|------|-------------|-----------------|
| 200 | OK | Successful GET, PUT, PATCH | `Response.json(data)` |
| 201 | Created | Successful POST that created a resource | `Response.json(user, { status: 201 })` |
| 204 | No Content | Successful DELETE with no body | `new Response(null, { status: 204 })` |

## Redirection (3xx)

| Code | Name | When to Use |
|------|------|-------------|
| 301 | Moved Permanently | Resource URL changed forever |
| 302 | Found | Temporary redirect |
| 307 | Temporary Redirect | Redirect preserving HTTP method |
| 308 | Permanent Redirect | Permanent redirect preserving method |

## Client Error (4xx)

| Code | Name | When to Use |
|------|------|-------------|
| 400 | Bad Request | Malformed JSON, wrong content type |
| 401 | Unauthorized | Not authenticated (missing/invalid token) |
| 403 | Forbidden | Authenticated but not authorized |
| 404 | Not Found | Resource doesn't exist |
| 405 | Method Not Allowed | Wrong HTTP method for endpoint |
| 409 | Conflict | Duplicate resource, concurrent modification |
| 422 | Unprocessable Entity | Valid JSON but invalid data (Zod validation error) |
| 429 | Too Many Requests | Rate limit exceeded |

## Server Error (5xx)

| Code | Name | When to Use |
|------|------|-------------|
| 500 | Internal Server Error | Unhandled exception (your bug) |
| 502 | Bad Gateway | Upstream service returned invalid response |
| 503 | Service Unavailable | Maintenance or overload |
| 504 | Gateway Timeout | Upstream service didn't respond in time |

## Common Confusion

- **400 vs 422**: 400 = can't parse the request. 422 = parsed fine, but data is invalid.
- **401 vs 403**: 401 = who are you? (not logged in). 403 = I know who you are, you can't do this.
- **404 vs 410**: 404 = might exist later. 410 = permanently gone.

## See Also

- `hub/error-handling.md` — Error response patterns
- `hub/api-route-patterns.md` — Route handler patterns
