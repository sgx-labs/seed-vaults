---
title: "Constant-Time Comparison — Preventing Timing Attacks"
tags: [authentication, timing-attacks, comparison, cryptography]
content_type: research
domain: security
---

# Constant-Time Comparison — Preventing Timing Attacks

String comparison operators like `===` or `==` short-circuit on the first mismatch. This timing difference reveals information about secret values.

## The Problem

```javascript
// VULNERABLE — Short-circuit comparison leaks timing information
function verifyToken(userToken, actualToken) {
  return userToken === actualToken;
  // "aaaa..." vs "xxxx..." returns false after comparing 1st character (fast)
  // "xaaa..." vs "xxxx..." returns false after comparing 2nd character (slower)
  // Attacker can guess one character at a time by measuring response time
}
```

## Secure: Constant-Time Comparison

### Node.js

```javascript
const crypto = require('crypto');

// SECURE — Always takes the same time regardless of where strings differ
function safeCompare(a, b) {
  if (typeof a !== 'string' || typeof b !== 'string') return false;
  if (a.length !== b.length) {
    // Still compare to maintain constant time, but will return false
    crypto.timingSafeEqual(Buffer.from(a), Buffer.from(a));
    return false;
  }
  return crypto.timingSafeEqual(Buffer.from(a), Buffer.from(b));
}
```

### Python

```python
import hmac

# SECURE — hmac.compare_digest is constant-time
def safe_compare(a: str, b: str) -> bool:
    return hmac.compare_digest(a.encode(), b.encode())
```

### Go

```go
import "crypto/subtle"

// SECURE — ConstantTimeCompare in Go
func safeCompare(a, b string) bool {
    return subtle.ConstantTimeCompare([]byte(a), []byte(b)) == 1
}
```

## When to Use Constant-Time Comparison

- API key/token verification
- Webhook signature validation
- HMAC comparison
- CSRF token validation
- Any comparison involving secrets

## When Standard Comparison Is Fine

- Non-secret values (usernames, email addresses)
- Values that are already public
- Hashed passwords (bcrypt.compare already handles this)

## Webhook Signature Verification Example

```javascript
// SECURE — Verify webhook signatures (e.g., Stripe, GitHub)
const crypto = require('crypto');

function verifyWebhookSignature(payload, signature, secret) {
  const expected = crypto
    .createHmac('sha256', secret)
    .update(payload)
    .digest('hex');

  // MUST use constant-time comparison
  return crypto.timingSafeEqual(
    Buffer.from(signature),
    Buffer.from(expected)
  );
}
```

## How to Test

1. Search codebase for `=== secret` or `== token` patterns with secrets
2. Verify all HMAC comparisons use constant-time functions
3. Check webhook handlers for timing-safe signature verification
4. Verify API key comparison uses crypto.timingSafeEqual or equivalent

## See Also

- hub/authentication.md
- research/owasp/cryptographic-failures.md
- research/authentication/jwt-security.md
