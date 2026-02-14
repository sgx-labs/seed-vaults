---
title: "JWT Security — Pitfalls and Best Practices"
tags: [authentication, jwt, tokens, refresh-tokens, token-rotation, revocation, rs256]
content_type: research
domain: security
---

# JWT Security — Pitfalls and Best Practices

JWTs are widely used but frequently misimplemented. Most JWT vulnerabilities come from incorrect validation, not flaws in the standard itself.

## JWT Validation Checklist

Every JWT validation MUST check:
1. **Signature** — Verify with the correct algorithm and key
2. **Expiration** (`exp`) — Reject expired tokens
3. **Issuer** (`iss`) — Verify the token came from your auth server
4. **Audience** (`aud`) — Verify the token is intended for your service
5. **Not Before** (`nbf`) — Token not valid before this time

## Vulnerable: No Algorithm Verification — DO NOT USE

```javascript
// VULNERABLE — Accepts any algorithm, including "none"
const decoded = jwt.decode(token); // decode without verify
// Or: jwt.verify(token, secret) without specifying algorithms
```

## Secure: Strict Verification

```javascript
// SECURE — Explicit algorithm, full claim validation
const jwt = require('jsonwebtoken');

function verifyToken(token) {
  try {
    return jwt.verify(token, publicKey, {
      algorithms: ['RS256'],           // Explicit algorithm
      issuer: 'https://auth.example.com',
      audience: 'https://api.example.com',
      clockTolerance: 30,              // 30 seconds clock skew
    });
  } catch (err) {
    return null; // Invalid token
  }
}
```

## Token Lifetime Strategy

| Token | Lifetime | Storage | Purpose |
|-------|----------|---------|---------|
| Access Token | 5-15 minutes | Memory / Authorization header | API access |
| Refresh Token | 7-30 days | httpOnly cookie | Obtain new access tokens |
| ID Token | 5-15 minutes | Memory | User identity claims |

## Refresh Token Rotation

```javascript
// SECURE — Refresh token rotation with reuse detection
app.post('/token/refresh', async (req, res) => {
  const refreshToken = req.cookies.refreshToken;
  const stored = await db.getRefreshToken(refreshToken);

  if (!stored) {
    // Token not found — may be reuse of rotated token
    // Revoke entire token family as precaution
    await db.revokeTokenFamily(stored?.familyId);
    return res.status(401).json({ error: 'Invalid refresh token' });
  }

  if (stored.used) {
    // Reuse detected — likely token theft
    await db.revokeTokenFamily(stored.familyId);
    logger.warn('auth.refresh_token_reuse', { familyId: stored.familyId });
    return res.status(401).json({ error: 'Token reuse detected' });
  }

  // Mark current token as used
  await db.markTokenUsed(refreshToken);

  // Issue new token pair
  const newAccessToken = generateAccessToken(stored.userId);
  const newRefreshToken = generateRefreshToken(stored.userId, stored.familyId);
  await db.storeRefreshToken(newRefreshToken);

  res.cookie('refreshToken', newRefreshToken, {
    httpOnly: true, secure: true, sameSite: 'strict', path: '/token/refresh'
  });
  res.json({ accessToken: newAccessToken });
});
```

## Token Revocation

JWTs are stateless by design — you cannot truly "revoke" them without server-side state. Options:

1. **Short-lived tokens** — The simplest approach; tokens expire quickly
2. **Blocklist** — Store revoked token IDs (jti) in Redis with TTL matching token expiry
3. **Token versioning** — Store a version counter per user; reject tokens with old version

## Common Mistakes

- Storing sensitive data in JWT payload (it is base64-encoded, NOT encrypted)
- Using symmetric signing (HS256) with a weak secret
- Not validating all claims (iss, aud, exp)
- Storing access tokens in localStorage (XSS-accessible)
- No refresh token rotation (stolen refresh token = permanent access)

## How to Test

1. Decode a JWT and check what data is in the payload
2. Try changing the algorithm to "none" and submitting
3. Verify expired tokens are rejected
4. Test with tokens from a different issuer
5. Verify refresh token rotation works (old token rejected after rotation)

## See Also

- hub/authentication.md
- research/authentication/session-management.md
- research/authentication/oauth-oidc.md
