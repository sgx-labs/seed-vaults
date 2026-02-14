---
title: "A07:2025 — Authentication Failures"
tags: [owasp, authentication, brute-force, credential-stuffing, session, user-enumeration, nist-800-63b]
content_type: research
domain: security
---

# A07:2025 — Authentication Failures

Confirmation of user identity, authentication, and session management is critical. Authentication failures allow attackers to impersonate users.

## Common Failures

- No rate limiting on login endpoints (enables brute force)
- Credential stuffing (reused passwords from breaches)
- Default or weak passwords allowed
- Missing MFA for sensitive operations
- Session tokens not invalidated on logout
- Password recovery via easily guessed security questions

## Vulnerable: No Rate Limiting (Express.js) — DO NOT USE

```javascript
// VULNERABLE — Unlimited login attempts allow brute-force attacks
app.post('/login', async (req, res) => {
  const user = await db.findUser(req.body.email);
  if (!user || !await bcrypt.compare(req.body.password, user.hash)) {
    return res.status(401).json({ error: 'Invalid credentials' });
  }
  req.session.userId = user.id;
  res.json({ success: true });
});
```

## Secure: Rate-Limited Login

```javascript
// SECURE — Rate limiting + session regeneration
const rateLimit = require('express-rate-limit');

const loginLimiter = rateLimit({
  windowMs: 15 * 60 * 1000, // 15 minutes
  max: 5,                    // 5 attempts per window
  message: { error: 'Too many login attempts. Try again later.' },
  standardHeaders: true,
  keyGenerator: (req) => req.body.email || req.ip,
});

app.post('/login', loginLimiter, async (req, res) => {
  const user = await db.findUser(req.body.email);
  if (!user || !await bcrypt.compare(req.body.password, user.hash)) {
    await db.incrementFailedAttempts(req.body.email);
    return res.status(401).json({ error: 'Invalid credentials' });
  }
  await db.resetFailedAttempts(req.body.email);
  req.session.regenerate(() => {
    req.session.userId = user.id;
    res.json({ success: true });
  });
});
```

## Vulnerable: User Enumeration — DO NOT USE

```python
# VULNERABLE — Different error messages reveal if email exists
@app.route('/login', methods=['POST'])
def login():
    user = db.find_user(request.json['email'])
    if not user:
        return jsonify({"error": "User not found"}), 401
    if not check_password(request.json['password'], user.hash):
        return jsonify({"error": "Wrong password"}), 401
```

## Secure: Consistent Error Messages

```python
# SECURE — Same error message regardless of failure reason
DUMMY_HASH = generate_dummy_hash()  # Prevents timing attacks

@app.route('/login', methods=['POST'])
@rate_limit(max_attempts=5, window=900)
def login():
    user = db.find_user(request.json['email'])
    stored_hash = user.hash if user else DUMMY_HASH
    if not user or not check_password(request.json['password'], stored_hash):
        return jsonify({"error": "Invalid credentials"}), 401
    session.regenerate()
    session['user_id'] = user.id
    return jsonify({"success": True})
```

## Password Policy (NIST SP 800-63B)

- Minimum 8 characters (allow up to 64+)
- Check against breached password lists (HaveIBeenPwned API)
- Do NOT require special characters (marginal security benefit)
- Do NOT force periodic rotation (NIST guidance since 2017)
- Require MFA for admin and sensitive accounts

## How to Test

1. Attempt rapid-fire login with wrong passwords — should be rate limited
2. Try valid email + wrong password vs invalid email — should get same error
3. Check if session ID changes after login (session fixation prevention)
4. Verify logout actually invalidates server-side session
5. Check password reset flow for token expiration and single-use enforcement

## See Also

- hub/authentication.md
- research/authentication/mfa-patterns.md
- research/authentication/password-reset-flows.md
- research/api-security/rate-limiting.md
