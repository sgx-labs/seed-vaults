---
title: "Passkeys and WebAuthn Implementation"
tags: [authentication, passkeys, webauthn, fido2, passwordless]
content_type: research
domain: security
---

# Passkeys and WebAuthn

FIDO2/WebAuthn provides phishing-resistant, passwordless authentication using public-key cryptography. Passkeys are synced FIDO2 credentials that work across devices.

## Why Passkeys Matter

- **Phishing-proof** — Credentials are origin-bound (cannot be used on fake sites)
- **No shared secrets** — Server stores public key only; nothing to steal
- **No password reuse** — Each site gets a unique key pair
- **Built-in MFA** — Possession + biometric/PIN in a single step
- **Supported everywhere** — Apple, Google, Microsoft all sync passkeys

## Registration Flow

```javascript
// SECURE — Server-side registration (using @simplewebauthn/server)
const {
  generateRegistrationOptions,
  verifyRegistrationResponse,
} = require('@simplewebauthn/server');

// Step 1: Generate registration options
app.post('/auth/register/options', authenticate, async (req, res) => {
  const user = req.user;
  const options = await generateRegistrationOptions({
    rpName: 'Example App',
    rpID: 'example.com',
    userID: user.id,
    userName: user.email,
    attestationType: 'none', // Simplest, sufficient for most apps
    authenticatorSelection: {
      residentKey: 'preferred',     // Enable passkeys
      userVerification: 'preferred', // Biometric/PIN if available
    },
  });

  // Store challenge for verification
  await db.storeChallenge(user.id, options.challenge);
  res.json(options);
});

// Step 2: Verify registration response
app.post('/auth/register/verify', authenticate, async (req, res) => {
  const user = req.user;
  const expectedChallenge = await db.getChallenge(user.id);

  const verification = await verifyRegistrationResponse({
    response: req.body,
    expectedChallenge,
    expectedOrigin: 'https://example.com',
    expectedRPID: 'example.com',
  });

  if (verification.verified) {
    // Store credential public key for future authentication
    await db.storeCredential(user.id, {
      credentialID: verification.registrationInfo.credentialID,
      publicKey: verification.registrationInfo.credentialPublicKey,
      counter: verification.registrationInfo.counter,
    });
    res.json({ verified: true });
  }
});
```

## Authentication Flow

```javascript
// Step 1: Generate authentication options
app.post('/auth/login/options', async (req, res) => {
  const options = await generateAuthenticationOptions({
    rpID: 'example.com',
    userVerification: 'preferred',
    // Optionally specify allowCredentials for known user
  });
  await db.storeChallenge(req.sessionID, options.challenge);
  res.json(options);
});

// Step 2: Verify authentication response
app.post('/auth/login/verify', async (req, res) => {
  const expectedChallenge = await db.getChallenge(req.sessionID);
  const credential = await db.getCredential(req.body.id);

  const verification = await verifyAuthenticationResponse({
    response: req.body,
    expectedChallenge,
    expectedOrigin: 'https://example.com',
    expectedRPID: 'example.com',
    authenticator: {
      credentialPublicKey: credential.publicKey,
      counter: credential.counter,
    },
  });

  if (verification.verified) {
    // Update counter to prevent replay
    await db.updateCounter(credential.id, verification.authenticationInfo.newCounter);
    req.session.userId = credential.userId;
    res.json({ verified: true });
  }
});
```

## Implementation Checklist

- [ ] Use a library (SimpleWebAuthn, py_webauthn) — do not implement from scratch
- [ ] Set rpID to your domain (not full URL)
- [ ] Store and verify challenge for every registration/authentication
- [ ] Update credential counter after each use (replay detection)
- [ ] Provide fallback auth for account recovery
- [ ] Allow users to register multiple credentials

## Gradual Migration Strategy

1. Offer passkey registration to existing users (optional)
2. Prompt for passkey creation after password login
3. Allow both password and passkey login
4. Once adoption is high, offer passwordless-only option

## See Also

- hub/authentication.md
- entities/auth-standards.md — FIDO2/WebAuthn standards
- research/authentication/mfa-patterns.md
