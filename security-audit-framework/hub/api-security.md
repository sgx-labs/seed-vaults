---
title: "API Security"
tags: [api-security, rate-limiting, cors, security-headers, input-validation, xss]
content_type: hub
domain: security
---

# API Security

API security encompasses every layer of protection for your HTTP endpoints, including security headers like Content-Security-Policy and HSTS, server-side input validation with schema enforcement, rate limiting to prevent brute force and DDoS, CORS configuration to control cross-origin access, secure file upload handling, WebSocket authentication, GraphQL query depth limiting, and SPA versus SSR security tradeoffs. This hub provides configuration examples, implementation patterns, and links to detailed research notes for each attack vector.

## Core Principles

1. **Validate everything** — Never trust client input, headers, or parameters
2. **Authenticate every request** — No anonymous endpoints unless explicitly intended
3. **Rate limit aggressively** — Abuse is the default, legitimate use is the exception
4. **Minimize response data** — Only return what the client needs

## Security Headers

| Header | Value | Purpose |
|--------|-------|---------|
| Content-Security-Policy | script-src 'self' | Prevents XSS via inline scripts |
| Strict-Transport-Security | max-age=63072000; includeSubDomains | Forces HTTPS |
| X-Content-Type-Options | nosniff | Prevents MIME-type sniffing |
| X-Frame-Options | DENY | Prevents clickjacking |
| Referrer-Policy | strict-origin-when-cross-origin | Controls referrer leakage |
| Permissions-Policy | camera=(), microphone=() | Restricts browser features |

See: research/api-security/security-headers.md

## Input Validation

- Validate type, length, format, and range on the server side
- Use allowlists over denylists
- Reject requests with unexpected fields (strict schema validation)
- Sanitize output, not just input (context-dependent encoding)

See: research/api-security/input-validation.md

## Rate Limiting

- Implement per-user, per-IP, and per-endpoint limits
- Use token bucket or sliding window algorithms
- Return 429 with Retry-After header
- Apply stricter limits to auth endpoints (5 attempts/minute)

See: research/api-security/rate-limiting.md

## CORS Configuration

- Never use `Access-Control-Allow-Origin: *` with credentials
- Validate Origin header against an allowlist
- Limit allowed methods and headers
- Set appropriate `Access-Control-Max-Age`

See: research/api-security/cors-configuration.md

## Research Notes

| Topic | Note |
|-------|------|
| Security Headers | research/api-security/security-headers.md |
| Input Validation | research/api-security/input-validation.md |
| Rate Limiting | research/api-security/rate-limiting.md |
| CORS Configuration | research/api-security/cors-configuration.md |
| File Upload Security | research/api-security/file-uploads.md |
| WebSocket Security | research/api-security/websocket-security.md |

## See Also

- hub/owasp-top-10.md — A04: Injection, A02: Security Misconfiguration
- hub/authentication.md — API auth patterns, JWT, session cookies
- hub/infrastructure.md — TLS configuration, secrets in transit
- hub/dependencies.md — Dependency scanning in API build pipelines
- research/owasp/injection.md — SQL/NoSQL/Command injection
- research/api-security/graphql-security.md — GraphQL-specific patterns
- research/api-security/spa-ssr-security.md — SPA vs SSR tradeoffs
