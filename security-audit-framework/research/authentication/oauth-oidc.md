---
title: "OAuth 2.1 and OpenID Connect Implementation"
tags: [authentication, oauth, oidc, authorization, tokens]
content_type: research
domain: security
---

# OAuth 2.1 and OpenID Connect

OAuth 2.1 is the modern authorization framework. OIDC adds authentication on top. Together they handle delegated access and federated identity.

## OAuth 2.1 Key Changes

- **PKCE required** for ALL clients (public and confidential)
- **Implicit grant removed** — was vulnerable to token leakage
- **Resource Owner Password grant removed** — antipattern
- **Bearer tokens in query strings prohibited** — use Authorization header
- **Refresh token rotation recommended**

## Authorization Code Flow with PKCE

```javascript
// Step 1: Generate PKCE challenge
const crypto = require('crypto');

function generatePKCE() {
  const verifier = crypto.randomBytes(32).toString('base64url');
  const challenge = crypto
    .createHash('sha256')
    .update(verifier)
    .digest('base64url');
  return { verifier, challenge };
}

// Step 2: Build authorization URL
const { verifier, challenge } = generatePKCE();
const authUrl = new URL('https://auth.example.com/authorize');
authUrl.searchParams.set('response_type', 'code');
authUrl.searchParams.set('client_id', CLIENT_ID);
authUrl.searchParams.set('redirect_uri', 'https://app.example.com/callback');
authUrl.searchParams.set('scope', 'openid profile email');
authUrl.searchParams.set('state', crypto.randomBytes(16).toString('hex'));
authUrl.searchParams.set('code_challenge', challenge);
authUrl.searchParams.set('code_challenge_method', 'S256');

// Step 3: Exchange code for tokens (server-side)
app.get('/callback', async (req, res) => {
  // Verify state parameter matches what we stored
  if (req.query.state !== storedState) {
    return res.status(400).json({ error: 'Invalid state' });
  }

  const tokenResponse = await fetch('https://auth.example.com/token', {
    method: 'POST',
    headers: { 'Content-Type': 'application/x-www-form-urlencoded' },
    body: new URLSearchParams({
      grant_type: 'authorization_code',
      code: req.query.code,
      redirect_uri: 'https://app.example.com/callback',
      client_id: CLIENT_ID,
      code_verifier: storedVerifier, // PKCE verifier
    }),
  });

  const tokens = await tokenResponse.json();
  // Validate ID token claims (iss, aud, exp, nonce)
  // Store tokens securely
});
```

## Common OAuth/OIDC Mistakes

| Mistake | Risk | Fix |
|---------|------|-----|
| Missing state parameter | CSRF | Always generate and validate state |
| No PKCE | Code interception | Use S256 PKCE for all clients |
| Open redirect_uri | Token theft | Exact-match redirect URI validation |
| Token in URL fragment | Leakage via Referer | Use code flow, not implicit |
| No nonce in OIDC | Replay attacks | Generate and validate nonce in ID token |

## Redirect URI Validation

```python
# SECURE — Exact match, no wildcards, no open redirects
ALLOWED_REDIRECT_URIS = [
    'https://app.example.com/callback',
    'https://app.example.com/auth/callback',
]

def validate_redirect_uri(redirect_uri):
    if redirect_uri not in ALLOWED_REDIRECT_URIS:
        raise ValueError('Invalid redirect_uri')
    return redirect_uri
```

## When to Use OAuth vs Direct Auth

- **Use OAuth 2.1 + OIDC when:** Integrating with third-party identity providers, building SSO, or needing delegated access
- **Use direct auth when:** Simple app with own user store, no third-party integration needed
- **Always use OIDC (not just OAuth) when:** You need to AUTHENTICATE users (know who they are), not just authorize access

## How to Test

1. Verify PKCE is enforced (try without code_verifier)
2. Test with missing or tampered state parameter
3. Try redirect_uri values not in the allowlist
4. Verify tokens have correct audience and issuer
5. Test token refresh flow and rotation

## See Also

- hub/authentication.md
- research/authentication/jwt-security.md
- entities/auth-standards.md
