---
title: "A09:2025 — Security Logging and Monitoring Failures"
tags: [owasp, logging, monitoring, detection, alerting]
content_type: research
domain: security
---

# A09:2025 — Security Logging and Monitoring Failures

Without proper logging and monitoring, breaches go undetected. The average time to detect a breach is still measured in months, not minutes.

## What Must Be Logged

- Authentication successes AND failures
- Authorization failures (access denied events)
- Input validation failures (potential attack probes)
- Application errors and exceptions
- Administrative actions (user creation, permission changes)
- Data access to sensitive records

## What Must NOT Be Logged

- Passwords (even failed attempts)
- Session tokens or API keys
- Credit card numbers or SSNs
- Personal health information
- Full request bodies containing PII

See: research/infrastructure/sensitive-data-logs.md

## Vulnerable: No Security Logging — DO NOT USE

```javascript
// VULNERABLE — No logging of security-relevant events
app.post('/login', async (req, res) => {
  const user = await db.findUser(req.body.email);
  if (!user || !await bcrypt.compare(req.body.password, user.hash)) {
    return res.status(401).json({ error: 'Invalid credentials' });
  }
  res.json({ token: generateToken(user) });
});
```

## Secure: Structured Security Logging

```javascript
// SECURE — Log security events with context
const logger = require('./logger'); // Structured logging (winston, pino, etc.)

app.post('/login', async (req, res) => {
  const user = await db.findUser(req.body.email);
  if (!user || !await bcrypt.compare(req.body.password, user.hash)) {
    logger.warn('auth.login_failed', {
      email: req.body.email,
      ip: req.ip,
      userAgent: req.get('user-agent'),
      timestamp: new Date().toISOString(),
    });
    return res.status(401).json({ error: 'Invalid credentials' });
  }

  logger.info('auth.login_success', {
    userId: user.id,
    ip: req.ip,
    timestamp: new Date().toISOString(),
  });
  res.json({ token: generateToken(user) });
});
```

## Secure: Python Structured Logging

```python
import structlog

logger = structlog.get_logger()

@app.route('/admin/users/<user_id>', methods=['DELETE'])
@require_role('admin')
def delete_user(user_id):
    logger.info('admin.user_deleted',
                actor_id=g.user.id,
                target_user_id=user_id,
                ip=request.remote_addr)
    db.delete_user(user_id)
    return jsonify({"status": "deleted"})
```

## Alerting Thresholds

| Event | Alert When |
|-------|-----------|
| Login failures | > 5 from same IP in 15 min |
| Login failures | > 3 for same account in 5 min |
| Authorization failures | Any admin endpoint access denied |
| Rate limit triggers | > 100 429s from same IP in 1 hour |
| Error rate spike | > 5% 500 errors in 5 min window |

## Log Format Best Practices

- Use structured logging (JSON) — not plaintext
- Include timestamp, event type, actor, target, outcome
- Use correlation IDs across services
- Ship logs to a centralized system (not just local files)
- Set retention policies (90 days minimum for security logs)

## How to Test

1. Trigger a failed login — verify it appears in logs with context
2. Access a forbidden resource — verify denial is logged
3. Check logs do NOT contain passwords, tokens, or PII
4. Verify alerts fire for brute-force patterns
5. Confirm logs are shipped to a centralized system

## See Also

- hub/owasp-top-10.md
- research/infrastructure/sensitive-data-logs.md
- research/infrastructure/incident-response.md
