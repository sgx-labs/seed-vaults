---
title: "HTTP Status Codes Reference"
tags: [http, status-codes, reference, entity, 2xx, 4xx, 5xx]
content_type: entity
pinned: true
domain: api-design
---

# HTTP Status Codes Reference

## 2xx Success

| Code | Name | When to Use |
|------|------|-------------|
| 200 | OK | Successful GET, PUT, PATCH, DELETE |
| 201 | Created | Successful POST that created a resource. Include `Location` header. |
| 202 | Accepted | Request accepted for async processing. Return job ID/status URL. |
| 204 | No Content | Successful DELETE or PUT with no response body needed. |

## 3xx Redirection

| Code | Name | When to Use |
|------|------|-------------|
| 301 | Moved Permanently | Resource URL changed permanently. Clients should update. |
| 302 | Found | Temporary redirect. Client should follow but keep using original URL. |
| 304 | Not Modified | Conditional GET -- resource hasn't changed since `If-None-Match`/`If-Modified-Since`. |

## 4xx Client Errors

| Code | Name | When to Use |
|------|------|-------------|
| 400 | Bad Request | Malformed request syntax, invalid JSON, wrong content type. |
| 401 | Unauthorized | Missing or invalid authentication credentials. |
| 403 | Forbidden | Authenticated but not authorized for this resource/action. |
| 404 | Not Found | Resource doesn't exist (or you don't want to reveal it exists). |
| 405 | Method Not Allowed | HTTP method not supported on this endpoint (e.g., DELETE on read-only). |
| 406 | Not Acceptable | Server can't produce a response matching Accept header. |
| 409 | Conflict | Request conflicts with current state (duplicate, concurrent modification). |
| 410 | Gone | Resource existed but has been permanently removed. Use for deprecated endpoints. |
| 415 | Unsupported Media Type | Content-Type not supported (e.g., XML when only JSON accepted). |
| 422 | Unprocessable Entity | Request is syntactically valid but semantically wrong (validation errors). |
| 429 | Too Many Requests | Rate limit exceeded. Include `Retry-After` header. |

## 5xx Server Errors

| Code | Name | When to Use |
|------|------|-------------|
| 500 | Internal Server Error | Unhandled exception. Your fault, not the client's. |
| 502 | Bad Gateway | Upstream service returned an invalid response. |
| 503 | Service Unavailable | Server is temporarily down (maintenance, overload). Include `Retry-After`. |
| 504 | Gateway Timeout | Upstream service didn't respond in time. |

## Common Mistakes

- **200 for everything** -- some APIs return 200 with `{"error": true}`. Don't.
- **401 vs 403 confusion** -- 401 = "who are you?" (not authenticated). 403 = "I know who you are but you can't do this" (not authorized).
- **404 vs 410** -- 404 = might come back or never existed. 410 = definitely gone forever.
- **400 vs 422** -- 400 = can't parse the request (bad JSON). 422 = parsed fine but the data is invalid.
- **500 for client errors** -- if the client sent bad data, that's 4xx, not 5xx.

## See Also

- `hub/error-handling.md` -- Error response format
- `hub/rest-fundamentals.md` -- Status codes in REST context
