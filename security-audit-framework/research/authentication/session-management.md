---
title: "Session Management Security"
tags: [authentication, sessions, cookies, csrf, fixation, httponly, samesite, redis]
content_type: research
domain: security
---

# Session Management Security

Sessions are the bridge between authentication and authorization. Poor session management undermines everything.

## Cookie Security Flags

Every session cookie MUST have these flags:

```
Set-Cookie: sessionId=abc123;
  Secure;        // Only sent over HTTPS
  HttpOnly;      // Not accessible via JavaScript
  SameSite=Lax;  // CSRF protection
  Path=/;        // Scope to entire site
  Max-Age=3600;  // 1 hour expiration
```

## Express.js Session Configuration

```javascript
// SECURE — Production session configuration
const session = require('express-session');
const RedisStore = require('connect-redis').default;

app.use(session({
  store: new RedisStore({ client: redisClient }),
  name: '__Host-sid',         // __Host- prefix enforces Secure + Path=/
  secret: process.env.SESSION_SECRET, // From secrets manager
  resave: false,
  saveUninitialized: false,
  cookie: {
    secure: true,             // HTTPS only
    httpOnly: true,           // No JavaScript access
    sameSite: 'lax',          // CSRF protection
    maxAge: 3600000,          // 1 hour
    domain: undefined,        // Do not set — use origin only
  },
}));
```

## Session Fixation Prevention

```javascript
// SECURE — Regenerate session ID after login
app.post('/login', async (req, res) => {
  const user = await authenticate(req.body);
  if (!user) return res.status(401).json({ error: 'Invalid credentials' });

  // Regenerate to prevent fixation
  req.session.regenerate((err) => {
    if (err) return res.status(500).json({ error: 'Session error' });
    req.session.userId = user.id;
    req.session.role = user.role;
    res.json({ success: true });
  });
});
```

## Session Invalidation on Logout

```javascript
// SECURE — Destroy server-side session AND clear cookie
app.post('/logout', (req, res) => {
  req.session.destroy((err) => {
    if (err) {
      logger.error('session.destroy_failed', { error: err.message });
    }
    res.clearCookie('__Host-sid');
    res.json({ success: true });
  });
});
```

## Timeout Strategy

| Timeout Type | Duration | Purpose |
|-------------|----------|---------|
| Absolute | 8-24 hours | Maximum session lifetime |
| Idle | 15-30 minutes | Inactivity expiration |
| Privilege | 5 minutes | Re-auth for sensitive operations |

## CSRF Protection

For `SameSite=Lax`, additional CSRF protection is needed for state-changing GET requests and cross-origin form submissions:

```javascript
// SECURE — CSRF token middleware
const csrf = require('csurf');
app.use(csrf({ cookie: false })); // Use session-based tokens

// In your form template
// <input type="hidden" name="_csrf" value="<%= csrfToken %>">
```

## How to Test

1. Check cookie flags in browser dev tools (Secure, HttpOnly, SameSite)
2. Verify session ID changes after login (fixation prevention)
3. Verify logout destroys server-side session (not just cookie)
4. Test idle timeout by waiting, then making a request
5. Verify session store is external (Redis, DB) — not in-memory

## See Also

- hub/authentication.md
- research/owasp/broken-access-control.md
- research/authentication/jwt-security.md
