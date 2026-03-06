---
title: "CORS Configuration"
tags: [cors, cross-origin, preflight, security, headers, browser]
content_type: hub
domain: api-design
---

# CORS Configuration

## Overview

Cross-Origin Resource Sharing (CORS) controls which websites can call your API from a browser. Without CORS headers, browsers block cross-origin requests by default. This is a browser security feature, not an API security feature -- server-to-server calls are unaffected.

## How CORS Works

1. Browser makes a request to a different origin (domain, port, or protocol)
2. Browser checks the response for CORS headers
3. If headers allow the origin, the browser delivers the response
4. If headers are missing or don't match, the browser blocks it

For non-simple requests (PUT, DELETE, custom headers), the browser sends a **preflight** OPTIONS request first.

## Key Headers

```
Access-Control-Allow-Origin: https://app.example.com
Access-Control-Allow-Methods: GET, POST, PUT, DELETE
Access-Control-Allow-Headers: Content-Type, Authorization
Access-Control-Max-Age: 86400
Access-Control-Allow-Credentials: true
Access-Control-Expose-Headers: X-Request-Id, X-RateLimit-Remaining
```

| Header | Purpose |
|--------|---------|
| `Allow-Origin` | Which origins can access the resource |
| `Allow-Methods` | Which HTTP methods are allowed |
| `Allow-Headers` | Which request headers are allowed |
| `Max-Age` | How long preflight results are cached (seconds) |
| `Allow-Credentials` | Whether cookies/auth headers are sent |
| `Expose-Headers` | Which response headers the browser can read |

## Configuration Patterns

### Single Origin (Most Secure)
```
Access-Control-Allow-Origin: https://app.example.com
```

### Multiple Origins (Dynamic)
Check the `Origin` request header against an allowlist. Return the matching origin.
```
Allowlist: [https://app.example.com, https://admin.example.com]

If request Origin in allowlist:
  Access-Control-Allow-Origin: {request Origin}
  Vary: Origin
Else:
  No CORS headers (browser blocks the request)
```

Always include `Vary: Origin` when the Allow-Origin header changes based on the request.

### Wildcard (Public APIs)
```
Access-Control-Allow-Origin: *
```
Cannot be used with `Allow-Credentials: true`. Use for genuinely public, anonymous endpoints only.

## Preflight Requests

Browsers send an OPTIONS request before "complex" requests:
- Methods other than GET, HEAD, POST
- Custom headers (Authorization, X-Custom-*)
- Content-Type other than form-urlencoded, multipart/form-data, text/plain

```
OPTIONS /api/users HTTP/1.1
Origin: https://app.example.com
Access-Control-Request-Method: PUT
Access-Control-Request-Headers: Content-Type, Authorization

HTTP/1.1 204 No Content
Access-Control-Allow-Origin: https://app.example.com
Access-Control-Allow-Methods: GET, POST, PUT, DELETE
Access-Control-Allow-Headers: Content-Type, Authorization
Access-Control-Max-Age: 86400
```

Set `Max-Age` high (86400 = 24 hours) to reduce preflight requests.

## Common Mistakes

- **Using `*` with credentials** -- browsers reject this combination. You must specify the exact origin.
- **Forgetting `Vary: Origin`** -- caches serve the wrong CORS headers to different origins.
- **Not handling OPTIONS** -- framework returns 404 or 405 for preflight, breaking all cross-origin requests.
- **Treating CORS as security** -- CORS is a browser feature. It doesn't protect your API from curl, Postman, or server-side requests. Use authentication for security.
- **Allowing all origins in production** -- `*` is fine for truly public APIs, but dangerous for anything with authentication.

## When NOT to Worry About CORS

- Server-to-server APIs (CORS only applies to browsers)
- Same-origin applications (frontend and API on the same domain)
- Mobile apps (not subject to CORS)

## See Also

- `hub/authentication.md` -- Auth headers interact with CORS credentials
- `entities/security-headers.md` -- Other security-related headers
- `research/api-security-checklist.md` -- CORS in the context of API security
