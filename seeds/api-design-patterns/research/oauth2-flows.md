---
title: "OAuth2 Flows Deep Dive"
tags: [oauth2, authorization-code, pkce, client-credentials, device-code, security, auth]
content_type: research
domain: api-design
---

# OAuth2 Flows Deep Dive

## The Core Idea

OAuth2 is a delegation protocol. A user grants a third-party application limited access to their data on a service -- without sharing their password. Different "flows" (grant types) handle different scenarios.

## Authorization Code Flow (with PKCE)

The default choice for web apps, mobile apps, and SPAs. PKCE (Proof Key for Code Exchange) prevents authorization code interception attacks.

```
1. Client generates code_verifier (random string) and code_challenge (SHA256 hash)

2. Client redirects user to authorization server:
   GET /authorize?
     response_type=code
     &client_id=abc
     &redirect_uri=https://app.com/callback
     &scope=read:users
     &state=random-csrf-token
     &code_challenge=sha256hash
     &code_challenge_method=S256

3. User logs in and consents

4. Authorization server redirects back with code:
   GET https://app.com/callback?code=xyz&state=random-csrf-token

5. Client exchanges code for tokens:
   POST /token
   grant_type=authorization_code
   &code=xyz
   &redirect_uri=https://app.com/callback
   &client_id=abc
   &code_verifier=original-random-string

6. Server returns:
   {
     "access_token": "eyJ...",
     "token_type": "Bearer",
     "expires_in": 3600,
     "refresh_token": "dGhpcyBpcyBhIHJlZnJlc2g..."
   }
```

**When to use**: Any app where a user interacts via a browser. This is the recommended flow for almost everything.

**PKCE is mandatory** for SPAs and mobile apps (no client secret). Recommended even for server-side apps as defense in depth.

## Client Credentials Flow

Machine-to-machine authentication. No user involved.

```
POST /token
  grant_type=client_credentials
  &client_id=abc
  &client_secret=secret123
  &scope=read:data

Response:
  {
    "access_token": "eyJ...",
    "token_type": "Bearer",
    "expires_in": 3600
  }
```

**When to use**: Backend services calling other backend services, cron jobs, automation scripts, any server-to-server communication where no user context is needed.

**When NOT to use**: Anything involving a user. There is no user identity in this flow.

## Device Code Flow

For devices that can't open a browser -- CLI tools, smart TVs, IoT devices.

```
1. Device requests a code:
   POST /device/code
   client_id=abc&scope=read:users

   Response:
   {
     "device_code": "abc123",
     "user_code": "WDJB-MJHT",
     "verification_uri": "https://auth.example.com/device",
     "expires_in": 600,
     "interval": 5
   }

2. Device displays to user:
   "Go to https://auth.example.com/device and enter code: WDJB-MJHT"

3. Device polls for completion:
   POST /token
   grant_type=urn:ietf:params:oauth:grant-type:device_code
   &device_code=abc123
   &client_id=abc

   Response (pending): {"error": "authorization_pending"}
   Response (success): {"access_token": "eyJ...", ...}
```

**When to use**: CLI tools (like `gh auth login`), smart TVs, devices without keyboards.

## Refresh Token Flow

Access tokens are short-lived. Refresh tokens get new access tokens without re-authentication.

```
POST /token
  grant_type=refresh_token
  &refresh_token=dGhpcyBpcyBhIHJlZnJlc2g
  &client_id=abc

Response:
  {
    "access_token": "new-eyJ...",
    "refresh_token": "new-dGhpcyBpcyBh...",
    "expires_in": 3600
  }
```

**Best practices**:
- Rotate refresh tokens on every use (one-time use)
- Store refresh tokens server-side, never in localStorage
- Set refresh token expiry (14-90 days)
- Revoke all refresh tokens on password change

## Flow Selection Guide

| Scenario | Flow | Why |
|----------|------|-----|
| Web app with backend | Auth Code + PKCE | Secure, handles user delegation |
| Single-page app (SPA) | Auth Code + PKCE | No client secret needed |
| Mobile app | Auth Code + PKCE | Uses custom URI scheme for redirect |
| CLI tool | Device Code | No browser on the device |
| Server-to-server | Client Credentials | No user involved |
| Smart TV / IoT | Device Code | Limited input capability |

## Common Mistakes

- **Using the Implicit flow** -- deprecated. Use Auth Code + PKCE instead.
- **Storing tokens in localStorage** -- vulnerable to XSS. Use httpOnly cookies or in-memory storage.
- **Long-lived access tokens** -- use short-lived access tokens (15 min) + refresh tokens.
- **Not validating the `state` parameter** -- opens you to CSRF attacks.
- **Exposing client secrets in SPAs** -- SPAs can't keep secrets. Use PKCE without a client secret.

## See Also

- `hub/authentication.md` -- Overview of all auth approaches
- `entities/oauth2-grant-types.md` -- Quick reference for grant types
- `research/api-security-checklist.md` -- Security implications of OAuth2
