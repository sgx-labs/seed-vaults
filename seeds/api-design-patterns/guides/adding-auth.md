---
title: "Adding Authentication to an Existing API"
tags: [guide, authentication, jwt, api-key, middleware, retrofit]
content_type: guide
domain: api-design
---

# Adding Authentication to an Existing API

## Overview

You have an API running without authentication. Now you need to add it. This guide walks through the process without breaking existing clients.

## Step 1: Choose Your Auth Strategy

| Situation | Strategy |
|-----------|----------|
| API consumed by your own frontend | JWT with refresh tokens |
| API consumed by other developers | API keys |
| API consumed by third-party apps on behalf of users | OAuth2 |
| All of the above | API keys for developers, JWT for end users |

See `hub/authentication.md` for detailed comparison.

## Step 2: Add Auth Middleware

Create middleware that runs before your route handlers:

```
function authMiddleware(request, response, next):
  token = extractToken(request)

  if token is null:
    return 401 {"title": "Authentication Required", "status": 401}

  claims = verifyToken(token)

  if claims is null:
    return 401 {"title": "Invalid Token", "status": 401}

  if claims.exp < now():
    return 401 {"title": "Token Expired", "status": 401}

  request.user = claims
  next()

function extractToken(request):
  header = request.headers["Authorization"]
  if header starts with "Bearer ":
    return header.substring(7)

  apiKey = request.headers["X-API-Key"]
  if apiKey is not null:
    return apiKey

  return null
```

## Step 3: Migration Strategy (Don't Break Existing Clients)

### Option A: Grace Period

1. Deploy auth middleware in "warn" mode -- log unauthenticated requests but allow them
2. Notify consumers: "Authentication required starting [date, 30+ days out]"
3. Monitor: track how many requests are unauthenticated
4. Enforce: switch to "deny" mode after the grace period

### Option B: Versioned Rollout

1. Create `/v2/` endpoints with auth required
2. Keep `/v1/` endpoints working without auth (with deprecation notice)
3. Set a sunset date for `/v1/`
4. Remove `/v1/` after sunset

### Option C: Header-Based Opt-In

1. If `Authorization` header is present, validate it
2. If absent, allow the request (temporarily)
3. Add `X-Auth-Required-After: 2025-03-01` response header
4. After the date, require auth on all requests

## Step 4: Create Auth Endpoints

```
POST /auth/register
  {"email": "alice@test.com", "password": "secure123"}
  -> 201 {"user_id": "usr_abc", "api_key": "sk_live_..."}

POST /auth/login
  {"email": "alice@test.com", "password": "secure123"}
  -> 200 {"access_token": "eyJ...", "refresh_token": "...", "expires_in": 900}

POST /auth/refresh
  {"refresh_token": "..."}
  -> 200 {"access_token": "new_eyJ...", "expires_in": 900}

POST /auth/logout
  -> 204 (invalidate refresh token)
```

## Step 5: Protect Specific Endpoints

Not every endpoint needs the same level of protection:

```
Public (no auth):
  GET  /health
  POST /auth/register
  POST /auth/login

Authenticated (any valid token):
  GET  /users/me
  GET  /projects
  POST /projects

Admin only (role check):
  GET  /admin/users
  DELETE /admin/users/:id
```

## Step 6: Security Checklist

- [ ] Hash passwords with bcrypt/argon2 (never store plaintext)
- [ ] Rate limit login endpoint (10 attempts per minute per IP)
- [ ] Set short access token expiry (15 minutes)
- [ ] Store refresh tokens in the database (revocable)
- [ ] Rotate refresh tokens on use
- [ ] Use HTTPS only
- [ ] Don't log tokens or passwords
- [ ] Add `X-Request-Id` for audit trail

## See Also

- `hub/authentication.md` -- Auth pattern details
- `hub/authorization.md` -- Role-based access control
- `research/oauth2-flows.md` -- OAuth2 implementation
- `entities/security-headers.md` -- Security headers to add
