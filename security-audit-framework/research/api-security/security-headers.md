---
title: "Security Headers — Complete Guide"
tags: [api-security, headers, csp, hsts, xss-protection]
content_type: research
domain: security
---

# Security Headers — Complete Guide

Security headers are your first line of defense against client-side attacks. They are free, easy to implement, and protect against entire classes of vulnerabilities.

## Essential Headers

### Content-Security-Policy (CSP)

The most powerful security header. Controls what resources the browser can load.

```
Content-Security-Policy: default-src 'self'; script-src 'self'; style-src 'self' 'unsafe-inline'; img-src 'self' data: https:; font-src 'self'; connect-src 'self' https://api.example.com; frame-ancestors 'none'; base-uri 'self'; form-action 'self';
```

**Start strict, loosen as needed:**
```javascript
// SECURE — Express.js with helmet
const helmet = require('helmet');
app.use(helmet.contentSecurityPolicy({
  directives: {
    defaultSrc: ["'self'"],
    scriptSrc: ["'self'"],              // No inline scripts
    styleSrc: ["'self'", "'unsafe-inline'"], // Inline styles (common need)
    imgSrc: ["'self'", "data:", "https:"],
    connectSrc: ["'self'", "https://api.example.com"],
    frameAncestors: ["'none'"],         // Prevent clickjacking
    baseUri: ["'self'"],                // Prevent base tag injection
    formAction: ["'self'"],             // Restrict form submissions
  },
}));
```

### Strict-Transport-Security (HSTS)

Forces HTTPS for all future requests to this domain.

```
Strict-Transport-Security: max-age=63072000; includeSubDomains; preload
```

**Warning:** Only enable `includeSubDomains` if ALL subdomains support HTTPS. `preload` is permanent — submit to hstspreload.org only when ready.

### Other Essential Headers

```javascript
// SECURE — All recommended headers via helmet
app.use(helmet({
  contentSecurityPolicy: { /* see above */ },
  hsts: { maxAge: 63072000, includeSubDomains: true },
  xContentTypeOptions: true,           // X-Content-Type-Options: nosniff
  frameguard: { action: 'deny' },      // X-Frame-Options: DENY
  referrerPolicy: {
    policy: 'strict-origin-when-cross-origin'
  },
  permissionsPolicy: {
    features: {
      camera: [],          // Deny camera access
      microphone: [],      // Deny microphone access
      geolocation: [],     // Deny geolocation
    }
  },
}));

// Remove X-Powered-By
app.disable('x-powered-by');
```

## Python/Django Configuration

```python
# SECURE — Django security headers
SECURE_HSTS_SECONDS = 63072000
SECURE_HSTS_INCLUDE_SUBDOMAINS = True
SECURE_CONTENT_TYPE_NOSNIFF = True
SECURE_BROWSER_XSS_FILTER = True  # Legacy, CSP is better
X_FRAME_OPTIONS = 'DENY'
SECURE_REFERRER_POLICY = 'strict-origin-when-cross-origin'

# CSP via django-csp
CSP_DEFAULT_SRC = ("'self'",)
CSP_SCRIPT_SRC = ("'self'",)
CSP_STYLE_SRC = ("'self'", "'unsafe-inline'")
```

## Go Configuration

```go
// SECURE — Security headers middleware in Go
func securityHeaders(next http.Handler) http.Handler {
    return http.HandlerFunc(func(w http.ResponseWriter, r *http.Request) {
        w.Header().Set("Content-Security-Policy",
            "default-src 'self'; script-src 'self'")
        w.Header().Set("Strict-Transport-Security",
            "max-age=63072000; includeSubDomains")
        w.Header().Set("X-Content-Type-Options", "nosniff")
        w.Header().Set("X-Frame-Options", "DENY")
        w.Header().Set("Referrer-Policy", "strict-origin-when-cross-origin")
        next.ServeHTTP(w, r)
    })
}
```

## How to Test

1. Use securityheaders.com to scan your site
2. Check browser dev tools Network tab for response headers
3. Verify CSP blocks inline scripts (test with `<script>alert(1)</script>`)
4. Verify HSTS is set with appropriate max-age
5. Use Mozilla Observatory for comprehensive scan

## See Also

- hub/api-security.md
- research/owasp/security-misconfiguration.md
- research/api-security/cors-configuration.md
