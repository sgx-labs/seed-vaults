---
title: "Authentication Patterns"
tags: [authentication, auth, oauth2, jwt, api-key, session, bearer, security]
content_type: hub
domain: api-design
---

# Authentication Patterns

## Overview

Authentication verifies identity -- "who are you?" Every API needs an auth strategy, but the right choice depends on your clients, security requirements, and architecture. This hub covers the four main approaches: API keys, OAuth2, JWT, and session tokens.

## API Keys

The simplest auth mechanism. A long random string passed in a header or query parameter.

```
GET /api/data HTTP/1.1
X-API-Key: sk_live_abc123def456
```

**When to use**: Server-to-server communication, public APIs with usage tracking, simple integrations, developer platforms.

**When NOT to use**: User-facing apps (keys can't represent user identity), anything requiring granular permissions per user, mobile apps (keys get extracted from binaries).

**Best practices**:
- Prefix keys to identify type: `sk_live_`, `sk_test_`, `pk_`
- Hash keys in your database -- store the hash, not the raw key
- Support key rotation -- allow multiple active keys per account
- Scope keys to specific permissions when possible

## OAuth2

The industry standard for delegated authorization. A user grants your app limited access to their data on another service.

```
Authorization: Bearer eyJhbGciOiJSUzI1NiIs...
```

**When to use**: Third-party integrations ("Sign in with Google"), APIs consumed by external developers, any scenario where a user delegates access.

**When NOT to use**: First-party apps talking to their own backend (simpler options exist), machine-to-machine without user context (use client credentials flow instead).

**Common flows**:
- **Authorization Code + PKCE** -- web apps, mobile apps, SPAs (the default choice)
- **Client Credentials** -- server-to-server, no user involved
- **Device Code** -- CLI tools, TVs, devices without browsers

See `research/oauth2-flows.md` for a deep dive on each flow.

## JWT (JSON Web Tokens)

Self-contained tokens that encode claims. The server can verify them without a database lookup.

```
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwicm9sZSI6ImFkbWluIn0.signature
```

**When to use**: Stateless APIs, microservices (tokens carry claims between services), short-lived access tokens in OAuth2 flows.

**When NOT to use**: Long-lived sessions (JWTs can't be revoked without a blocklist), storing sensitive data in claims (tokens are encoded, not encrypted), scenarios requiring immediate session termination.

**Best practices**:
- Keep tokens short-lived (15 minutes for access tokens)
- Use refresh tokens for long sessions (stored server-side, revocable)
- Never store sensitive data in JWT claims
- Use RS256 (asymmetric) for distributed systems, HS256 for single-server
- Validate all claims: `exp`, `iss`, `aud`, `nbf`

## Session Tokens

Server-side sessions with an opaque token stored in a cookie.

```
Set-Cookie: session_id=abc123; HttpOnly; Secure; SameSite=Strict
```

**When to use**: Traditional web apps, same-domain APIs, scenarios requiring instant session revocation.

**When NOT to use**: Cross-domain APIs (cookie restrictions), mobile apps, third-party consumers, microservices.

## Comparison

| Factor | API Key | OAuth2 | JWT | Session |
|--------|---------|--------|-----|---------|
| Complexity | Low | High | Medium | Low |
| Revocability | Easy | Easy | Hard* | Easy |
| Scalability | High | High | High | Medium |
| User identity | No | Yes | Yes | Yes |
| Stateless | Yes | Depends | Yes | No |
| Best for | Server-to-server | Delegation | Microservices | Web apps |

*JWTs require a blocklist or short expiry + refresh tokens for effective revocation.

## Decision Framework

1. **Server-to-server, no user?** -> API key or OAuth2 client credentials
2. **Third-party access to user data?** -> OAuth2 Authorization Code + PKCE
3. **First-party web app, same domain?** -> Session tokens (simplest) or JWT + refresh
4. **Microservices talking internally?** -> JWT (claims propagate between services)
5. **Mobile or SPA?** -> OAuth2 Authorization Code + PKCE with refresh tokens
6. **CLI tool?** -> OAuth2 Device Code flow or API key

## Research Notes

- `research/oauth2-flows.md` -- Deep dive on OAuth2 flows (auth code, PKCE, client credentials)

## See Also

- `hub/authorization.md` -- What authenticated users are allowed to do
- `entities/security-headers.md` -- Security headers for auth endpoints
- `guides/adding-auth.md` -- Step-by-step guide to adding auth
- `decisions/auth-strategy-decision.md` -- Template for choosing your auth approach
