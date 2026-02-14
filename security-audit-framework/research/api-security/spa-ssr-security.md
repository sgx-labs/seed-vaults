---
title: "SPA vs SSR Security Implications"
tags: [api-security, spa, ssr, xss, csrf, architecture]
content_type: research
domain: security
---

# SPA vs SSR Security Implications

The choice between Single-Page Application (SPA) and Server-Side Rendering (SSR) has significant security implications.

## Attack Surface Comparison

| Risk | SPA (React, Vue, Angular) | SSR (Next.js SSR, Django, Rails) |
|------|---------------------------|----------------------------------|
| XSS | Higher (client-side rendering) | Lower (server-controlled output) |
| CSRF | Lower (API + token auth) | Higher (cookie-based forms) |
| Token storage | Challenging (where to put JWT?) | Easier (httpOnly cookies) |
| CSP complexity | Higher (dynamic rendering) | Lower (static templates) |
| Data exposure | Higher (API returns full objects) | Lower (server filters data) |
| Bundle security | Exposed client code | Server code is private |

## SPA Security Concerns

### Token Storage Problem

```javascript
// VULNERABLE — JWT in localStorage is accessible via XSS
// Any script running on the page can read localStorage

// SECURE — Use httpOnly cookie for refresh token, memory for access token
// Server sets refresh token as httpOnly cookie:
// Set-Cookie: refreshToken=xxx; HttpOnly; Secure; SameSite=Strict; Path=/api/refresh

// Client stores access token in a JavaScript variable (memory only)
let accessToken = null;

async function login(credentials) {
  const response = await fetch('/api/login', {
    method: 'POST',
    credentials: 'include',
    body: JSON.stringify(credentials),
  });
  const data = await response.json();
  accessToken = data.accessToken; // Memory only, lost on page refresh
}

// Silent refresh on page load to recover session
async function refreshAuth() {
  const response = await fetch('/api/refresh', {
    method: 'POST',
    credentials: 'include',
  });
  if (response.ok) {
    const data = await response.json();
    accessToken = data.accessToken;
  }
}
```

### API Data Over-Exposure

```javascript
// VULNERABLE — API returns more data than the client needs
// GET /api/users/123 returns:
// { id, name, email, internalNotes, adminFlags, ... }

// SECURE — API returns only what the client needs
// GET /api/users/123 returns:
// { id, name, avatar }
// Server-side: serialize with allowlists, not denylists
```

## SSR Security Concerns

### CSRF Protection

SSR applications with cookie-based auth need explicit CSRF protection. SPAs with token-based auth (Authorization header) are naturally CSRF-resistant because tokens are not auto-sent by the browser.

### Template Injection

```python
# Always enable autoescape (default in modern frameworks)
# Jinja2 autoescapes by default in Flask
# Django templates autoescape by default
# Never use |safe or markupsafe.Markup with user input
```

## Hybrid Architecture (SSR + SPA)

Modern frameworks like Next.js, Nuxt, and SvelteKit blend SSR and SPA:

**Security benefits:**
- Server components keep sensitive logic server-side
- httpOnly cookies for auth (SSR handles login pages)
- Client gets only the data it needs
- CSP can be stricter (less inline JS needed)

**Security risks:**
- More complex architecture = more attack surface
- Server components may accidentally expose secrets to client bundles
- Hydration mismatches can create XSS opportunities

## Best Practices Regardless of Architecture

1. **Validate on the server** — Client validation is for UX only
2. **Use CSP headers** — Restrict script sources
3. **Encode output** — For the correct context (HTML, URL, JS)
4. **Minimize API responses** — Return only necessary fields
5. **Separate auth from API calls** — Use httpOnly cookies for auth state
6. **Review client bundle** — Check that no secrets leaked into JS bundle

## How to Test

1. Search JavaScript bundles for hardcoded secrets or API keys
2. Check if XSS in any input field can steal authentication tokens
3. Verify API endpoints return only necessary data fields
4. Test CSRF protection on all state-changing operations
5. Review CSP header for overly permissive rules

## See Also

- hub/api-security.md
- research/owasp/injection.md — XSS prevention
- research/authentication/session-management.md
- research/api-security/security-headers.md
