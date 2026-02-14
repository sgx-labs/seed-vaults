---
title: "Secure Password Reset Flows"
tags: [authentication, password-reset, tokens, email-verification, rate-limiting, user-enumeration]
content_type: research
domain: security
---

# Secure Password Reset Flows

Password reset is one of the most attacked authentication features. Every step must be designed to resist abuse.

## The Secure Flow

1. User enters email address
2. Server always responds "If that email exists, you will receive a reset link" (no enumeration)
3. Server generates a cryptographically random, time-limited token
4. Token is hashed before storage (like a password)
5. Email contains a single-use reset link
6. User clicks link, enters new password
7. Token is verified, marked as used, all other sessions invalidated

## Token Generation

```javascript
// SECURE — Cryptographic token with hash storage
const crypto = require('crypto');
const bcrypt = require('bcrypt');

async function createResetToken(userId) {
  // Generate high-entropy token
  const token = crypto.randomBytes(32).toString('hex');

  // Hash before storing (treat like a password)
  const hashedToken = await bcrypt.hash(token, 10);

  await db.storeResetToken({
    userId,
    tokenHash: hashedToken,
    expiresAt: new Date(Date.now() + 30 * 60 * 1000), // 30 minutes
    used: false,
  });

  return token; // Send this in the email, NOT the hash
}
```

## Reset Endpoint

```javascript
// SECURE — Full reset flow with validation
app.post('/auth/reset-password', async (req, res) => {
  const { token, newPassword } = req.body;

  // Find all unexpired, unused tokens (may need to check multiple)
  const storedTokens = await db.getUnusedResetTokens();

  let matchedToken = null;
  for (const stored of storedTokens) {
    if (stored.expiresAt < new Date()) continue;
    if (await bcrypt.compare(token, stored.tokenHash)) {
      matchedToken = stored;
      break;
    }
  }

  if (!matchedToken) {
    return res.status(400).json({ error: 'Invalid or expired reset link' });
  }

  // Validate new password
  if (!isStrongPassword(newPassword)) {
    return res.status(400).json({ error: 'Password does not meet requirements' });
  }

  // Update password
  const newHash = await bcrypt.hash(newPassword, 12);
  await db.updatePassword(matchedToken.userId, newHash);

  // Invalidate the token
  await db.markTokenUsed(matchedToken.id);

  // Invalidate all existing sessions
  await db.destroyAllSessions(matchedToken.userId);

  logger.info('auth.password_reset', { userId: matchedToken.userId });
  res.json({ success: true, message: 'Password updated. Please log in.' });
});
```

## Common Mistakes

| Mistake | Risk | Fix |
|---------|------|-----|
| Token in URL query string | Logged in server logs, Referer header | Use POST form, short-lived tokens |
| Sequential/predictable tokens | Token guessing | Use crypto.randomBytes(32) |
| Token stored in plaintext | DB compromise reveals tokens | Hash tokens before storing |
| No expiration | Permanent reset capability | 30-minute maximum |
| Token reuse allowed | Extended attack window | Mark as used after single use |
| No session invalidation | Old sessions remain active | Destroy all sessions on reset |
| Different response for valid/invalid email | User enumeration | Same response regardless |

## Rate Limiting

```javascript
// SECURE — Rate limit reset requests
const resetLimiter = rateLimit({
  windowMs: 60 * 60 * 1000, // 1 hour
  max: 3,                    // 3 reset requests per hour
  keyGenerator: (req) => req.body.email || req.ip,
  message: { error: 'Too many reset attempts. Try again later.' },
});

app.post('/auth/forgot-password', resetLimiter, async (req, res) => {
  // Always return success (prevent enumeration)
  res.json({ message: 'If that email exists, you will receive a reset link.' });

  // Send email asynchronously
  const user = await db.findByEmail(req.body.email);
  if (user) {
    const token = await createResetToken(user.id);
    await sendResetEmail(user.email, token);
  }
});
```

## How to Test

1. Request a reset — verify response is the same for valid and invalid emails
2. Use a reset token twice — second use should fail
3. Wait for token to expire — should fail
4. After password reset, verify old sessions are invalidated
5. Check that reset tokens are not in server logs

## See Also

- hub/authentication.md
- research/authentication/password-hashing.md
- research/owasp/authentication-failures.md
