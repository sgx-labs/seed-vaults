---
title: "A02:2025 — Security Misconfiguration"
tags: [owasp, misconfiguration, headers, defaults, hardening]
content_type: research
domain: security
---

# A02:2025 — Security Misconfiguration

Moved from #5 (2021) to #2 (2025). The most common root cause of breaches. Default configurations are almost never secure.

## Common Misconfigurations

- Default credentials left unchanged
- Unnecessary features enabled (debug mode, directory listing)
- Missing security headers
- Overly permissive CORS
- Cloud storage buckets set to public
- Verbose error messages exposing stack traces
- Unused open ports and services

## Vulnerable: Debug Mode in Production (Python/Django)

```python
# VULNERABLE — settings.py in production
DEBUG = True
ALLOWED_HOSTS = ['*']
```

**Why it is vulnerable:** Debug mode exposes full stack traces, database queries, and configuration details to attackers. `ALLOWED_HOSTS = ['*']` allows host header attacks.

## Secure: Production Configuration

```python
# SECURE — settings.py in production
DEBUG = False
ALLOWED_HOSTS = ['app.example.com']
SECRET_KEY = os.environ.get('DJANGO_SECRET_KEY')  # From secrets manager

# Security headers
SECURE_HSTS_SECONDS = 63072000
SECURE_SSL_REDIRECT = True
SESSION_COOKIE_SECURE = True
CSRF_COOKIE_SECURE = True
SECURE_CONTENT_TYPE_NOSNIFF = True
```

## Vulnerable: Express.js Default Config

```javascript
// VULNERABLE — Information leakage
const app = express();
// Default: X-Powered-By: Express header is sent
// No security headers configured
```

## Secure: Express.js Hardened Config

```javascript
// SECURE — Hardened with helmet
const helmet = require('helmet');
const app = express();

app.disable('x-powered-by');
app.use(helmet({
  contentSecurityPolicy: {
    directives: {
      defaultSrc: ["'self'"],
      scriptSrc: ["'self'"],
    }
  },
  hsts: { maxAge: 63072000, includeSubDomains: true },
}));

// Custom error handler — no stack traces
app.use((err, req, res, next) => {
  console.error(err.stack); // Log internally
  res.status(500).json({ error: 'Internal server error' }); // Generic to client
});
```

## Vulnerable: Default Cloud Storage (AWS S3)

```json
{
  "Effect": "Allow",
  "Principal": "*",
  "Action": "s3:GetObject",
  "Resource": "arn:aws:s3:::my-bucket/*"
}
```

## Secure: Restricted Cloud Storage

```json
{
  "Effect": "Allow",
  "Principal": { "AWS": "arn:aws:iam::ACCOUNT_ID:role/app-role" },
  "Action": "s3:GetObject",
  "Resource": "arn:aws:s3:::my-bucket/*",
  "Condition": {
    "IpAddress": { "aws:SourceIp": "10.0.0.0/8" }
  }
}
```

## How to Test

1. Check for default credentials on admin interfaces
2. Look for debug/diagnostic endpoints (e.g., /debug, /info, /health with sensitive data)
3. Scan for missing security headers (securityheaders.com)
4. Review cloud IAM policies for overly broad permissions
5. Use `nmap` to find unnecessary open ports

## See Also

- hub/owasp-top-10.md
- research/api-security/security-headers.md
- research/infrastructure/cloud-security.md
