---
title: "Content-Type Headers Reference"
tags: [content-type, headers, mime, json, xml, multipart, reference, entity]
content_type: entity
domain: api-design
---

# Content-Type Headers Reference

## Common API Content Types

| Content-Type | Used For |
|-------------|----------|
| `application/json` | JSON request/response bodies (most APIs) |
| `application/xml` | XML request/response bodies (legacy APIs) |
| `application/x-www-form-urlencoded` | HTML form data, OAuth2 token requests |
| `multipart/form-data` | File uploads with metadata |
| `text/plain` | Plain text responses |
| `text/html` | HTML responses (documentation pages) |
| `text/event-stream` | Server-Sent Events (SSE) |
| `application/octet-stream` | Binary data (file downloads) |
| `application/pdf` | PDF documents |
| `application/gzip` | Gzip-compressed data |

## JSON Variants

| Content-Type | Used For |
|-------------|----------|
| `application/json` | Standard JSON |
| `application/problem+json` | RFC 7807 error responses |
| `application/vnd.api+json` | JSON:API specification |
| `application/hal+json` | HAL (Hypertext Application Language) |
| `application/json-patch+json` | JSON Patch (RFC 6902) |
| `application/merge-patch+json` | JSON Merge Patch (RFC 7386) |

## Content Negotiation

Clients request a specific format via the `Accept` header:

```
GET /users/123 HTTP/1.1
Accept: application/json

GET /users/123 HTTP/1.1
Accept: application/xml
```

Server responds with `Content-Type` matching the format it chose:
```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
```

If the server can't produce the requested format: `406 Not Acceptable`.

## Character Encoding

Always specify charset for text-based content types:
```
Content-Type: application/json; charset=utf-8
Content-Type: text/html; charset=utf-8
```

UTF-8 is the default for JSON (per RFC 8259), but being explicit prevents issues.

## Best Practices

- Always validate the `Content-Type` of incoming requests
- Return `415 Unsupported Media Type` for unexpected content types
- Use `application/json` for API responses (not `text/json`)
- Set `Content-Type` on every response, including errors
- Use `application/problem+json` for RFC 7807 error responses

## See Also

- `hub/rest-fundamentals.md` -- Content types in REST context
- `hub/caching.md` -- Content negotiation and caching (Vary header)
- `hub/cors.md` -- Content types that trigger CORS preflight
