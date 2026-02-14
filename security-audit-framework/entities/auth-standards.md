---
title: "Authentication Standards — Entity Reference"
tags: [oauth, oidc, fido2, webauthn, passkeys, totp, authentication-standards]
content_type: entity
domain: security
---

# Authentication Standards Reference

## OAuth 2.1

OAuth 2.1 consolidates and simplifies OAuth 2.0 with security best practices baked in.

**Key changes from OAuth 2.0:**
- PKCE required for all clients (not just public clients)
- Implicit grant removed (was vulnerable to token leakage)
- Resource Owner Password grant removed
- Bearer tokens in query strings prohibited
- Refresh token rotation recommended

**Status:** Draft specification, widely adopted in practice.

## OpenID Connect (OIDC)

Identity layer on top of OAuth 2.0. Adds the ID Token (JWT) for authentication.

**Key components:**
- ID Token — JWT containing user identity claims
- UserInfo Endpoint — Additional user profile data
- Discovery — `.well-known/openid-configuration` for automatic setup

**Use when:** You need to know WHO the user is, not just authorize access.

## FIDO2 / WebAuthn

The standard for passwordless authentication using public-key cryptography.

**Components:**
- **WebAuthn** — W3C browser API for creating/using credentials
- **CTAP2** — Protocol for external authenticators (security keys)
- **Passkeys** — Synced FIDO2 credentials (cross-device)

**Security properties:**
- Phishing-resistant (origin-bound credentials)
- No shared secrets (public-key cryptography)
- Biometric-capable (fingerprint, face recognition)
- Replay-resistant (challenge-response protocol)

**Current state (2025-2026):**
- All major browsers support WebAuthn
- Apple, Google, Microsoft sync passkeys across devices
- Recommended for both consumer and enterprise authentication

## TOTP / HOTP

Time-based and HMAC-based One-Time Passwords for MFA.

| Standard | RFC | Mechanism |
|----------|-----|-----------|
| TOTP | RFC 6238 | Time-based (30-second windows) |
| HOTP | RFC 4226 | Counter-based |

**Best practices:**
- TOTP preferred over HOTP (self-expiring)
- Use SHA-256 or SHA-512 (not SHA-1) where supported
- Provide backup codes during enrollment
- Consider phishing-resistant alternatives (WebAuthn)

## Comparison Matrix

| Standard | Phishing Resistant | MFA | Passwordless | Complexity |
|----------|-------------------|-----|--------------|------------|
| Password + TOTP | No | Yes | No | Low |
| OAuth 2.1 + OIDC | Depends on IdP | Depends | Depends | Medium |
| FIDO2/Passkeys | Yes | Built-in | Yes | Medium |
| SMS OTP | No (SIM swap) | Yes | No | Low |

## See Also

- hub/authentication.md — Implementation patterns
- research/authentication/oauth-oidc.md — OAuth/OIDC implementation
- research/authentication/passkeys-webauthn.md — Passkey implementation
- research/authentication/mfa-patterns.md — MFA design patterns
