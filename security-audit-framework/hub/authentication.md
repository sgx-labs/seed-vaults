---
title: "Authentication & Session Management"
tags: [authentication, session, oauth, jwt, passwords]
content_type: hub
domain: security
---

# Authentication & Session Management

Authentication is the single most critical security boundary. Get it wrong and nothing else matters.

## Core Principles

1. **Never roll your own auth** — Use battle-tested libraries and identity providers
2. **Defense in depth** — MFA, rate limiting, and monitoring together
3. **Least privilege** — Every token and session gets minimum required permissions
4. **Fail closed** — Ambiguous auth state = deny access

## Password Storage

| Algorithm | Status | Notes |
|-----------|--------|-------|
| Argon2id | Recommended | Memory-hard, resistant to GPU attacks |
| bcrypt | Acceptable | Cost factor >= 12, max 72-byte input |
| scrypt | Acceptable | Memory-hard alternative |
| PBKDF2 | Legacy | Only with HMAC-SHA256, 600K+ iterations |
| MD5/SHA1/SHA256 | NEVER | Not password hashing algorithms |

See: research/authentication/password-hashing.md

## Session Management

- Generate session IDs with CSPRNG (minimum 128 bits of entropy)
- Set cookie flags: `Secure`, `HttpOnly`, `SameSite=Lax` (or Strict)
- Implement absolute timeout (max 24h) and idle timeout (15-30min)
- Regenerate session ID after authentication state change
- Invalidate server-side on logout (not just client cookie deletion)

See: research/authentication/session-management.md

## Token-Based Auth (JWT)

- Short-lived access tokens (5-15 min)
- Longer-lived refresh tokens stored securely (httpOnly cookie or secure storage)
- Always validate: signature, expiration, issuer, audience
- Use asymmetric signing (RS256/ES256) for distributed systems
- Implement token revocation for logout and compromise scenarios

See: research/authentication/jwt-security.md

## Modern Standards

| Standard | Use Case | Research Note |
|----------|----------|---------------|
| OAuth 2.1 | Delegated authorization | research/authentication/oauth-oidc.md |
| OIDC | Federated identity | research/authentication/oauth-oidc.md |
| FIDO2/WebAuthn | Passwordless/Passkeys | research/authentication/passkeys-webauthn.md |
| TOTP/HOTP | MFA second factor | research/authentication/mfa-patterns.md |

## Common Mistakes

- Storing passwords in plaintext or reversible encryption
- Using JWT for sessions without revocation capability
- Not rate-limiting login endpoints
- Password reset tokens that do not expire
- Comparing secrets with `==` instead of constant-time comparison

## See Also

- hub/api-security.md — API authentication patterns
- research/authentication/password-reset-flows.md
- research/authentication/rbac-patterns.md
- entities/auth-standards.md
