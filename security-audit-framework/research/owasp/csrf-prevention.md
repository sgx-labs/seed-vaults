---
title: "CSRF Prevention Patterns"
tags: [owasp, csrf, tokens, samesite, cross-site]
content_type: research
domain: security
---

# CSRF Prevention Patterns

Cross-Site Request Forgery tricks a user's browser into making requests to your application using their active session. The browser automatically sends cookies, making the request appear legitimate.

## How CSRF Works

1. User logs into your-app.example.com (session cookie set)
2. User visits evil-site.example.com
3. Evil site contains a form targeting your application
4. Browser sends the form WITH the user's session cookie
5. Your server sees a valid session and processes the request

## Defense Layers

### 1. SameSite Cookie Attribute (Primary)

```javascript
// SECURE — SameSite cookies prevent most CSRF
app.use(session({
  cookie: {
    sameSite: 'lax',   // Blocks cross-site POST requests
    secure: true,       // HTTPS only
    httpOnly: true,     // No JavaScript access
  }
}));
```

**SameSite values:**
- `Strict` — Cookie never sent cross-site (may break legitimate links)
- `Lax` — Cookie sent on top-level GET navigations (recommended default)
- `None` — Cookie always sent (requires `Secure` flag; NOT recommended)

### 2. CSRF Tokens (Defense in Depth)

```javascript
// SECURE — Synchronizer token pattern
const csrf = require('csurf');
const csrfProtection = csrf({ cookie: false }); // Session-based, not cookie

app.get('/form', csrfProtection, (req, res) => {
  res.render('form', { csrfToken: req.csrfToken() });
});

app.post('/transfer', csrfProtection, (req, res) => {
  // csurf middleware validates the token automatically
  processTransfer(req.body);
});
```

```html
<!-- In the form template -->
<form method="POST" action="/transfer">
  <input type="hidden" name="_csrf" value="<%= csrfToken %>">
  <input type="text" name="amount">
  <button type="submit">Transfer</button>
</form>
```

### 3. Custom Header Verification

```javascript
// SECURE — Require custom header (browsers block cross-origin custom headers)
app.use('/api', (req, res, next) => {
  if (req.method !== 'GET' && req.method !== 'HEAD') {
    if (req.headers['x-requested-with'] !== 'XMLHttpRequest') {
      return res.status(403).json({ error: 'Missing required header' });
    }
  }
  next();
});
```

### 4. Origin/Referer Validation

```javascript
// SECURE — Check Origin header matches expected values
function validateOrigin(req, res, next) {
  const origin = req.headers.origin || req.headers.referer;
  if (!origin) {
    return res.status(403).json({ error: 'Missing origin' });
  }
  const allowed = ['https://app.example.com'];
  const parsed = new URL(origin);
  if (!allowed.includes(parsed.origin)) {
    return res.status(403).json({ error: 'Invalid origin' });
  }
  next();
}
```

## When CSRF Is NOT a Concern

- Token-based auth (JWT in Authorization header) — not auto-sent
- API-only backends with no cookie authentication
- `SameSite=Strict` cookies (no cross-site requests at all)

## When CSRF IS a Concern

- Cookie-based session authentication
- `SameSite=Lax` with sensitive GET endpoints
- `SameSite=None` cookies (always add CSRF tokens)
- Any form submission with cookie auth

## How to Test

1. Create an HTML page on a different origin with a form targeting your app
2. Submit the form while logged in — should be blocked
3. Verify CSRF tokens are present on all state-changing forms
4. Check SameSite attribute on all session cookies
5. Test that API endpoints reject requests without proper headers

## See Also

- hub/api-security.md
- research/authentication/session-management.md
- research/api-security/cors-configuration.md
