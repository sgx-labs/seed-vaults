---
title: "Handling Sensitive Data in Logs"
tags: [infrastructure, logging, pii, data-protection, compliance]
content_type: research
domain: security
---

# Handling Sensitive Data in Logs

Logs are a goldmine for attackers. If your logs contain secrets, tokens, or PII, a log aggregation breach becomes a data breach.

## What Must NEVER Be Logged

| Category | Examples | Why |
|----------|---------|-----|
| Credentials | Passwords, API keys, tokens | Direct account compromise |
| Session data | Session IDs, JWTs, cookies | Session hijacking |
| Financial | Credit card numbers, bank accounts | PCI DSS violation |
| Health data | Diagnoses, medications | HIPAA violation |
| Personal data | SSN, full DOB, biometrics | GDPR/privacy violation |
| Secrets | Encryption keys, private keys | Full system compromise |

## Vulnerable: Logging Sensitive Data — DO NOT USE

```javascript
// VULNERABLE — Logs the entire request body including passwords
app.post('/login', (req, res) => {
  logger.info('Login attempt', { body: req.body });
  // Logs: { body: { email: "user@example.com", password: "s3cr3t!" } }
});
```

```javascript
// VULNERABLE — Logging authorization headers
app.use((req, res, next) => {
  logger.info('Request', {
    path: req.path,
    headers: req.headers, // Contains Authorization: Bearer eyJhbG...
  });
  next();
});
```

## Secure: Sanitized Logging

```javascript
// SECURE — Explicitly log only safe fields
app.post('/login', (req, res) => {
  logger.info('Login attempt', {
    email: req.body.email,
    ip: req.ip,
    userAgent: req.get('user-agent'),
    // Password explicitly excluded
  });
});
```

```javascript
// SECURE — Middleware that sanitizes headers
function sanitizeHeaders(headers) {
  const safe = { ...headers };
  const REDACTED_HEADERS = ['authorization', 'cookie', 'x-api-key'];
  for (const header of REDACTED_HEADERS) {
    if (safe[header]) {
      safe[header] = '[REDACTED]';
    }
  }
  return safe;
}
```

## PII Masking Utility

```javascript
// SECURE — Mask PII in log output
function maskPII(data) {
  if (typeof data === 'string') {
    // Mask email addresses
    data = data.replace(
      /[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}/g,
      (match) => match[0] + '***@' + match.split('@')[1]
    );
    // Mask credit card numbers (simple pattern)
    data = data.replace(/\b\d{4}[\s-]?\d{4}[\s-]?\d{4}[\s-]?\d{4}\b/g,
      (match) => '****-****-****-' + match.slice(-4));
  }
  return data;
}
```

## Python Logging Filter

```python
# SECURE — Custom logging filter to redact sensitive fields
import logging
import re

class SensitiveDataFilter(logging.Filter):
    SENSITIVE_PATTERNS = [
        (re.compile(r'password["\s:=]+["\']?([^"\'\s,}]+)'), 'password=[REDACTED]'),
        (re.compile(r'token["\s:=]+["\']?([^"\'\s,}]+)'), 'token=[REDACTED]'),
        (re.compile(r'Bearer\s+\S+'), 'Bearer [REDACTED]'),
    ]

    def filter(self, record):
        msg = record.getMessage()
        for pattern, replacement in self.SENSITIVE_PATTERNS:
            msg = pattern.sub(replacement, msg)
        record.msg = msg
        record.args = ()
        return True

# Apply to all loggers
logging.getLogger().addFilter(SensitiveDataFilter())
```

## Log Retention Policy

| Log Type | Retention | Reason |
|----------|-----------|--------|
| Security/auth logs | 1 year | Compliance, forensics |
| Application logs | 90 days | Debugging, monitoring |
| Access logs | 90 days | Analytics, security |
| Debug logs | 7-30 days | High volume, low retention value |

## Checklist

- [ ] Audit all logger.info/warn/error calls for sensitive data
- [ ] Implement PII masking/redaction middleware
- [ ] Never log request bodies or full headers without filtering
- [ ] Add sensitive data detection to CI (Semgrep rules)
- [ ] Set log retention policies per compliance requirements
- [ ] Encrypt logs at rest and in transit
- [ ] Restrict log access to authorized personnel only

## How to Test

1. Search logs for patterns: email addresses, "password", "Bearer", credit card formats
2. Make a login request and verify the password is not in any log
3. Check that error responses do not include sensitive context
4. Verify log aggregation service encrypts data at rest
5. Run Semgrep rules for logging sensitive data patterns

## See Also

- hub/infrastructure.md
- research/owasp/logging-monitoring-failures.md
- hub/compliance.md — GDPR, HIPAA logging requirements
