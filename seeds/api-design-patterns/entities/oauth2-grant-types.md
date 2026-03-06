---
title: "OAuth2 Grant Types — Quick Reference"
tags: [oauth2, grant-types, authorization-code, pkce, client-credentials, device-code, reference, entity]
content_type: entity
domain: api-design
---

# OAuth2 Grant Types — Quick Reference

## Active Grant Types

| Grant Type | RFC | User Involved? | Client Type | Use Case |
|-----------|-----|----------------|-------------|----------|
| Authorization Code + PKCE | RFC 7636 | Yes | Public or confidential | Web apps, mobile, SPAs |
| Client Credentials | RFC 6749 | No | Confidential (server) | Server-to-server |
| Device Authorization | RFC 8628 | Yes (separate device) | Public | CLI, TV, IoT |
| Refresh Token | RFC 6749 | No (extends existing) | Any | Renewing access tokens |

## Deprecated Grant Types (Do NOT Use)

| Grant Type | Why Deprecated | Use Instead |
|-----------|---------------|-------------|
| Implicit | Token exposed in URL, no refresh tokens | Auth Code + PKCE |
| Resource Owner Password | Client handles user credentials directly | Auth Code + PKCE |

## Quick Decision

```
Need user authorization?
  Yes -> Can open a browser?
    Yes -> Authorization Code + PKCE
    No  -> Device Authorization
  No  -> Is it server-to-server?
    Yes -> Client Credentials
    No  -> Probably Auth Code + PKCE
```

## Token Types

| Token | Lifetime | Storage | Revocable? |
|-------|----------|---------|-----------|
| Access Token | Short (15 min) | Memory or httpOnly cookie | Via blocklist |
| Refresh Token | Long (14-90 days) | Server-side (DB) | Yes (delete from DB) |
| ID Token (OIDC) | Short (matches access) | Memory | N/A (not sent to API) |

## See Also

- `research/oauth2-flows.md` -- Detailed flow walkthroughs
- `hub/authentication.md` -- When to use OAuth2 vs other approaches
