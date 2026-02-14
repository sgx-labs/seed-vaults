---
title: "Multi-Factor Authentication Patterns"
tags: [authentication, mfa, totp, sms, security-keys]
content_type: research
domain: security
---

# Multi-Factor Authentication Patterns

MFA requires two or more of: something you know (password), something you have (device/key), something you are (biometric). Not all MFA is equal.

## MFA Strength Ranking

| Method | Phishing Resistant | SIM Swap Safe | Convenience | Recommendation |
|--------|-------------------|---------------|-------------|----------------|
| FIDO2/Passkeys | Yes | Yes | High | Best |
| Hardware security key | Yes | Yes | Medium | Excellent |
| Authenticator app (TOTP) | No | Yes | Medium | Good |
| Push notification | Partial | Yes | High | Good (with number matching) |
| SMS OTP | No | No | High | Avoid if possible |
| Email OTP | No | N/A | Medium | Avoid if possible |

## TOTP Implementation

```javascript
// SECURE — TOTP with speakeasy
const speakeasy = require('speakeasy');
const QRCode = require('qrcode');

// Step 1: Generate secret during MFA enrollment
app.post('/mfa/setup', authenticate, async (req, res) => {
  const secret = speakeasy.generateSecret({
    name: `ExampleApp:${req.user.email}`,
    issuer: 'ExampleApp',
    length: 32,
  });

  // Store secret (encrypted) — do NOT send the raw secret again after setup
  await db.storeMFASecret(req.user.id, encrypt(secret.base32));

  // Generate QR code for authenticator app
  const qrDataUrl = await QRCode.toDataURL(secret.otpauth_url);
  res.json({ qrCode: qrDataUrl, manualKey: secret.base32 });
});

// Step 2: Verify TOTP during login
app.post('/mfa/verify', async (req, res) => {
  const secret = decrypt(await db.getMFASecret(req.session.pendingUserId));

  const verified = speakeasy.totp.verify({
    secret: secret,
    encoding: 'base32',
    token: req.body.code,
    window: 1, // Allow 1 step before/after (30-second tolerance)
  });

  if (!verified) {
    return res.status(401).json({ error: 'Invalid code' });
  }

  req.session.userId = req.session.pendingUserId;
  req.session.mfaVerified = true;
  delete req.session.pendingUserId;
  res.json({ success: true });
});
```

## Backup Codes

```javascript
// SECURE — Generate backup codes during MFA setup
function generateBackupCodes(count = 10) {
  const crypto = require('crypto');
  const codes = [];
  for (let i = 0; i < count; i++) {
    codes.push(crypto.randomBytes(4).toString('hex')); // 8-char hex codes
  }
  return codes;
}

// Store hashed backup codes (treat like passwords)
async function storeBackupCodes(userId, codes) {
  const bcrypt = require('bcrypt');
  const hashed = await Promise.all(
    codes.map(code => bcrypt.hash(code, 10))
  );
  await db.storeBackupCodes(userId, hashed);
}
```

## MFA Enrollment Best Practices

1. Require re-authentication before MFA setup/changes
2. Show backup codes ONCE during enrollment — require user to confirm
3. Store MFA secrets encrypted at rest
4. Allow multiple MFA methods (TOTP + backup codes + security key)
5. Log all MFA-related events (enrollment, verification, removal)

## When to Require MFA

- Admin/elevated accounts: ALWAYS
- Financial operations: ALWAYS
- Password changes: Re-verify current session
- Account recovery: Require backup codes or security key
- All users: Strongly recommended, mandatory for B2B

## How to Test

1. Verify TOTP codes work with authenticator apps (Google Authenticator, Authy)
2. Test with expired codes (should fail)
3. Verify backup codes work and are single-use
4. Test MFA bypass is not possible by skipping the verification step
5. Verify MFA secret cannot be retrieved after initial enrollment

## See Also

- hub/authentication.md
- research/authentication/passkeys-webauthn.md
- research/authentication/password-reset-flows.md
