---
title: "API Security Checklist (OWASP API Top 10)"
tags: [security, owasp, checklist, vulnerabilities, injection, authentication, authorization]
content_type: research
domain: api-design
---

# API Security Checklist (OWASP API Top 10)

## The Core Idea

API security is different from web security. APIs expose business logic directly, accept structured data, and are consumed by automated clients. The OWASP API Security Top 10 identifies the most critical API vulnerabilities. This checklist maps each risk to concrete actions.

## OWASP API Top 10 (2023)

### API1: Broken Object Level Authorization (BOLA)

User A can access User B's data by changing an ID in the URL.

```
GET /api/users/123/orders    # User A's orders
GET /api/users/456/orders    # User B's orders (should be forbidden)
```

**Fix**:
- [ ] Every endpoint checks that the authenticated user owns the requested resource
- [ ] Authorization logic is centralized, not scattered across handlers
- [ ] Use UUIDs instead of sequential IDs (makes guessing harder, but don't rely on this for security)

### API2: Broken Authentication

Weak or missing authentication allows unauthorized access.

**Fix**:
- [ ] All endpoints require authentication (except explicitly public ones)
- [ ] Rate limit login/signup endpoints aggressively
- [ ] Use strong token generation (cryptographic randomness)
- [ ] Implement account lockout after failed attempts
- [ ] Never expose tokens in URLs or logs

### API3: Broken Object Property Level Authorization

API returns more data than the user should see, or allows modifying fields they shouldn't.

```json
// Response includes admin-only field
{"name": "Alice", "email": "...", "role": "admin", "salary": 150000}

// Request modifies a protected field
PATCH /users/123 {"role": "admin"}  // Should be rejected
```

**Fix**:
- [ ] Explicitly define which fields are returned per role
- [ ] Whitelist writable fields -- reject unknown fields in requests
- [ ] Never return full database objects directly

### API4: Unrestricted Resource Consumption

No limits on request size, frequency, or complexity.

**Fix**:
- [ ] Rate limiting on all endpoints
- [ ] Maximum request body size
- [ ] Maximum page size for pagination
- [ ] Query complexity limits (GraphQL)
- [ ] File upload size limits
- [ ] Timeout for long-running operations

### API5: Broken Function Level Authorization

Regular users can access admin endpoints.

**Fix**:
- [ ] Admin endpoints use separate auth middleware
- [ ] Role checks happen at the middleware level, not just the UI
- [ ] Test authorization for every endpoint, not just the happy path

### API6: Unrestricted Access to Sensitive Business Flows

Automated abuse of business logic (bulk account creation, inventory hoarding).

**Fix**:
- [ ] Rate limit sensitive operations (purchase, signup, password reset)
- [ ] CAPTCHA for public-facing flows
- [ ] Device fingerprinting for abuse detection
- [ ] Monitor for unusual patterns

### API7: Server-Side Request Forgery (SSRF)

API fetches URLs provided by the user, allowing internal network access.

```
POST /api/fetch-url {"url": "http://169.254.169.254/metadata"}  // AWS metadata
```

**Fix**:
- [ ] Validate and sanitize user-provided URLs
- [ ] Block requests to internal IP ranges (10.x, 172.16.x, 192.168.x, 169.254.x)
- [ ] Use allowlists for permitted domains
- [ ] Don't follow redirects from user-provided URLs

### API8: Security Misconfiguration

Default configs, unnecessary features enabled, missing security headers.

**Fix**:
- [ ] Disable debug endpoints in production
- [ ] Remove default credentials
- [ ] Set security headers (see `entities/security-headers.md`)
- [ ] Disable unnecessary HTTP methods
- [ ] Review CORS configuration (see `hub/cors.md`)
- [ ] Keep dependencies updated

### API9: Improper Inventory Management

Old API versions, undocumented endpoints, shadow APIs.

**Fix**:
- [ ] Maintain an API inventory (every endpoint documented)
- [ ] Deprecate and remove old versions on a schedule
- [ ] Monitor for undocumented endpoints receiving traffic
- [ ] Same security standards for all versions

### API10: Unsafe Consumption of APIs

Your API trusts data from third-party APIs without validation.

**Fix**:
- [ ] Validate and sanitize all data received from third-party APIs
- [ ] Use timeouts and circuit breakers for external calls
- [ ] Don't trust third-party responses -- they can be compromised
- [ ] Log and monitor third-party API interactions

## General Security Checklist

- [ ] HTTPS everywhere (no HTTP, no exceptions)
- [ ] Security headers on all responses
- [ ] Input validation on all endpoints
- [ ] Output encoding to prevent injection
- [ ] Audit logging for sensitive operations
- [ ] Secrets management (no hardcoded keys)
- [ ] Dependency scanning for known vulnerabilities
- [ ] Regular penetration testing

## See Also

- `hub/authentication.md` -- Auth patterns
- `hub/authorization.md` -- Authorization models
- `hub/cors.md` -- CORS configuration
- `hub/request-validation.md` -- Input validation
- `entities/security-headers.md` -- Security headers reference
