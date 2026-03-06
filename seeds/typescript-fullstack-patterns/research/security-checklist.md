---
title: "Security Checklist"
tags: [security, xss, csrf, sql-injection, csp, headers, authentication, authorization, owasp]
content_type: research
domain: typescript-fullstack
---

# Security Checklist

## The Core Idea

Fullstack TypeScript apps have attack surface on every layer: client (XSS), server (injection), auth (session hijacking), and infrastructure (misconfig). This checklist covers the critical items. Review it before every launch.

## Authentication & Sessions

- [ ] Passwords hashed with bcrypt/argon2 (never SHA-256, never plaintext)
- [ ] Session tokens are cryptographically random (>= 128 bits)
- [ ] Cookies set with `HttpOnly`, `Secure`, `SameSite=Lax` (or Strict)
- [ ] Session expiration implemented (idle timeout + absolute timeout)
- [ ] Rate limiting on login/signup/password-reset endpoints
- [ ] Account lockout after repeated failed attempts
- [ ] No sensitive data in JWTs (they're encoded, not encrypted)

## Cross-Site Scripting (XSS)

React escapes JSX by default. But these bypass React's protection:

```typescript
// DANGEROUS — renders raw HTML
<div dangerouslySetInnerHTML={{ __html: userContent }} />

// SAFE — use a sanitizer
import DOMPurify from "isomorphic-dompurify";
<div dangerouslySetInnerHTML={{ __html: DOMPurify.sanitize(userContent) }} />
```

- [ ] Never use `dangerouslySetInnerHTML` without sanitization
- [ ] Sanitize user content before storing in database
- [ ] Set Content-Security-Policy header (see below)
- [ ] Escape user input in server-rendered HTML

## Cross-Site Request Forgery (CSRF)

Server actions in Next.js have built-in CSRF protection (origin checking). API routes do NOT.

- [ ] Server actions preferred for mutations (built-in CSRF protection)
- [ ] API routes validate `Origin` or `Referer` header for mutations
- [ ] `SameSite=Lax` on session cookies (prevents most CSRF)
- [ ] CSRF tokens for any non-server-action mutation from forms

## SQL Injection

Prisma and Drizzle parameterize queries by default. But raw queries are vulnerable:

```typescript
// DANGEROUS — string interpolation in raw SQL
await db.$queryRawUnsafe(`SELECT * FROM users WHERE id = '${userId}'`);

// SAFE — parameterized query
await db.$queryRaw`SELECT * FROM users WHERE id = ${userId}`;
```

- [ ] Never use string interpolation in SQL queries
- [ ] Use ORM parameterized queries exclusively
- [ ] If using `$queryRawUnsafe`, validate input rigorously
- [ ] Database user has minimal privileges (no DROP, no superuser)

## Security Headers

```typescript
// next.config.ts
const securityHeaders = [
  { key: "X-Frame-Options", value: "DENY" },
  { key: "X-Content-Type-Options", value: "nosniff" },
  { key: "Referrer-Policy", value: "strict-origin-when-cross-origin" },
  { key: "Permissions-Policy", value: "camera=(), microphone=(), geolocation=()" },
  {
    key: "Content-Security-Policy",
    value: [
      "default-src 'self'",
      "script-src 'self' 'unsafe-inline' 'unsafe-eval'", // Tighten in production
      "style-src 'self' 'unsafe-inline'",
      "img-src 'self' data: https:",
      "font-src 'self'",
      "connect-src 'self' https:",
      "frame-ancestors 'none'",
    ].join("; "),
  },
  {
    key: "Strict-Transport-Security",
    value: "max-age=63072000; includeSubDomains; preload",
  },
];

const nextConfig = {
  async headers() {
    return [{ source: "/(.*)", headers: securityHeaders }];
  },
};
```

## Environment Variables

- [ ] Secrets never in client bundles (no `NEXT_PUBLIC_` prefix for secrets)
- [ ] `.env` files in `.gitignore`
- [ ] Validate env vars at build time (t3-env)
- [ ] Rotate secrets regularly
- [ ] Different secrets per environment (dev/staging/prod)

## API Security

- [ ] Input validation on every endpoint (Zod)
- [ ] Rate limiting on all public endpoints
- [ ] Auth required on all non-public endpoints
- [ ] CORS restricted to known origins
- [ ] Request body size limits
- [ ] File upload type and size validation
- [ ] No sensitive data in error messages (production)

## Dependency Security

- [ ] `pnpm audit` in CI pipeline
- [ ] Dependabot or Renovate for automated updates
- [ ] Lock file committed (`pnpm-lock.yaml`)
- [ ] Minimal dependencies (audit every new package)

## Common Mistakes

1. **Trusting client-side validation** — always validate on the server
2. **Exposing stack traces in production** — configure error pages
3. **Using `NEXT_PUBLIC_` for secrets** — these are embedded in the client bundle
4. **No rate limiting** — bots will abuse your forms and APIs
5. **Overly permissive CORS** — `Access-Control-Allow-Origin: *` on authenticated APIs

## See Also

- `hub/authentication-patterns.md` — Auth implementation
- `hub/api-route-patterns.md` — API security patterns
- `hub/error-handling.md` — Safe error messages
- `entities/env-variables-patterns.md` — Environment variable security
