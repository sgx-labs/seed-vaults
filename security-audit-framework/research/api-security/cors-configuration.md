---
title: "CORS Configuration — Secure Patterns"
tags: [api-security, cors, cross-origin, headers]
content_type: research
domain: security
---

# CORS Configuration — Secure Patterns

Cross-Origin Resource Sharing (CORS) controls which origins can access your API. Misconfigured CORS is a common vulnerability.

## The Rules

1. **Never use `Access-Control-Allow-Origin: *` with credentials**
2. **Validate the Origin header against an allowlist**
3. **Never reflect the Origin header blindly**
4. **Limit allowed methods and headers**
5. **Set appropriate `Access-Control-Max-Age`**

## Vulnerable: Wildcard with Credentials — DO NOT USE

```javascript
// VULNERABLE — Allows ANY origin to send authenticated requests
app.use((req, res, next) => {
  res.header('Access-Control-Allow-Origin', '*');
  res.header('Access-Control-Allow-Credentials', 'true'); // Contradicts *
  next();
});
```

## Vulnerable: Origin Reflection — DO NOT USE

```javascript
// VULNERABLE — Reflects any origin, including attacker-controlled
app.use((req, res, next) => {
  res.header('Access-Control-Allow-Origin', req.headers.origin);
  res.header('Access-Control-Allow-Credentials', 'true');
  next();
});
```

## Secure: Allowlist Validation

```javascript
// SECURE — Explicit origin allowlist
const cors = require('cors');

const ALLOWED_ORIGINS = [
  'https://app.example.com',
  'https://admin.example.com',
];

app.use(cors({
  origin: (origin, callback) => {
    // Allow requests with no origin (mobile apps, server-to-server)
    if (!origin) return callback(null, true);

    if (ALLOWED_ORIGINS.includes(origin)) {
      callback(null, origin);
    } else {
      callback(new Error('Not allowed by CORS'));
    }
  },
  credentials: true,
  methods: ['GET', 'POST', 'PUT', 'DELETE'],
  allowedHeaders: ['Content-Type', 'Authorization'],
  maxAge: 86400, // 24 hours preflight cache
}));
```

## Secure: Python/Flask

```python
# SECURE — Flask-CORS with explicit origins
from flask_cors import CORS

ALLOWED_ORIGINS = [
    'https://app.example.com',
    'https://admin.example.com',
]

CORS(app,
     origins=ALLOWED_ORIGINS,
     supports_credentials=True,
     methods=['GET', 'POST', 'PUT', 'DELETE'],
     allow_headers=['Content-Type', 'Authorization'],
     max_age=86400)
```

## Secure: Go

```go
// SECURE — CORS middleware in Go
func corsMiddleware(next http.Handler) http.Handler {
    allowedOrigins := map[string]bool{
        "https://app.example.com":   true,
        "https://admin.example.com": true,
    }

    return http.HandlerFunc(func(w http.ResponseWriter, r *http.Request) {
        origin := r.Header.Get("Origin")
        if allowedOrigins[origin] {
            w.Header().Set("Access-Control-Allow-Origin", origin)
            w.Header().Set("Access-Control-Allow-Credentials", "true")
            w.Header().Set("Access-Control-Allow-Methods",
                "GET, POST, PUT, DELETE")
            w.Header().Set("Access-Control-Allow-Headers",
                "Content-Type, Authorization")
            w.Header().Set("Access-Control-Max-Age", "86400")
        }

        if r.Method == "OPTIONS" {
            w.WriteHeader(http.StatusNoContent)
            return
        }
        next.ServeHTTP(w, r)
    })
}
```

## When Wildcard Is Acceptable

`Access-Control-Allow-Origin: *` is safe ONLY when:
- The endpoint returns purely public data
- No cookies or Authorization headers are involved
- Examples: public APIs, CDN assets, open data feeds

## How to Test

1. Send a request with a malicious Origin header — should be blocked
2. Verify `Access-Control-Allow-Origin` is not `*` with credentials
3. Check that preflight (OPTIONS) returns correct headers
4. Verify allowed methods are restricted
5. Use browser dev tools to test cross-origin requests

## See Also

- hub/api-security.md
- research/api-security/security-headers.md
- research/owasp/security-misconfiguration.md
