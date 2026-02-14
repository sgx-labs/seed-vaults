---
title: "Rate Limiting — Implementation Patterns"
tags: [api-security, rate-limiting, ddos, abuse-prevention]
content_type: research
domain: security
---

# Rate Limiting — Implementation Patterns

Rate limiting prevents abuse, brute force, and denial of service. Every public API endpoint needs it.

## Strategy by Endpoint Type

| Endpoint | Limit | Window | Key |
|----------|-------|--------|-----|
| Login | 5 | 15 min | Email + IP |
| Password reset | 3 | 1 hour | Email + IP |
| Registration | 5 | 1 hour | IP |
| API (authenticated) | 1000 | 1 hour | User ID |
| API (unauthenticated) | 100 | 1 hour | IP |
| File upload | 10 | 1 hour | User ID |

## Express.js Implementation

```javascript
// SECURE — Multi-tier rate limiting
const rateLimit = require('express-rate-limit');
const RedisStore = require('rate-limit-redis');

// Global API rate limit
const apiLimiter = rateLimit({
  store: new RedisStore({ sendCommand: (...args) => redisClient.sendCommand(args) }),
  windowMs: 60 * 60 * 1000, // 1 hour
  max: 1000,
  standardHeaders: true,      // RateLimit-* headers
  legacyHeaders: false,
  message: { error: 'Rate limit exceeded. Try again later.' },
  keyGenerator: (req) => req.user?.id || req.ip,
});

// Strict auth endpoint limit
const authLimiter = rateLimit({
  store: new RedisStore({ sendCommand: (...args) => redisClient.sendCommand(args) }),
  windowMs: 15 * 60 * 1000, // 15 minutes
  max: 5,
  keyGenerator: (req) => `${req.body.email}:${req.ip}`,
  message: { error: 'Too many attempts. Try again in 15 minutes.' },
});

app.use('/api/', apiLimiter);
app.post('/auth/login', authLimiter);
```

## Python/Flask Implementation

```python
# SECURE — Flask-Limiter with Redis backend
from flask_limiter import Limiter
from flask_limiter.util import get_remote_address

limiter = Limiter(
    get_remote_address,
    app=app,
    storage_uri="redis://localhost:6379",
    default_limits=["1000 per hour"],
)

@app.route('/auth/login', methods=['POST'])
@limiter.limit("5 per 15 minutes", key_func=lambda: request.json.get('email', get_remote_address()))
def login():
    pass

@app.route('/api/data')
@limiter.limit("100 per hour")
def get_data():
    pass
```

## Go Implementation

```go
// SECURE — Token bucket rate limiter in Go
import "golang.org/x/time/rate"

// Per-IP rate limiter map
var visitors = make(map[string]*rate.Limiter)
var mu sync.Mutex

func getVisitor(ip string) *rate.Limiter {
    mu.Lock()
    defer mu.Unlock()
    limiter, exists := visitors[ip]
    if !exists {
        limiter = rate.NewLimiter(rate.Every(time.Second), 10) // 10 req/sec burst
        visitors[ip] = limiter
    }
    return limiter
}

func rateLimitMiddleware(next http.Handler) http.Handler {
    return http.HandlerFunc(func(w http.ResponseWriter, r *http.Request) {
        limiter := getVisitor(r.RemoteAddr)
        if !limiter.Allow() {
            w.Header().Set("Retry-After", "60")
            http.Error(w, "Rate limit exceeded", http.StatusTooManyRequests)
            return
        }
        next.ServeHTTP(w, r)
    })
}
```

## Response Headers

```
HTTP/1.1 429 Too Many Requests
Retry-After: 900
RateLimit-Limit: 5
RateLimit-Remaining: 0
RateLimit-Reset: 1640000000
```

## Common Mistakes

- Rate limiting only by IP (shared NATs, proxies)
- Not using a distributed store (in-memory resets on restart/scale)
- Missing rate limiting on auth endpoints
- Revealing exact limits (helps attackers calibrate)
- Not rate limiting internal/service-to-service calls

## How to Test

1. Send requests exceeding the limit — should get 429
2. Verify Retry-After header is present on 429 responses
3. Test that limits apply per-user, not just globally
4. Verify limits work across multiple server instances (distributed store)
5. Test with different IPs to confirm per-IP limits

## See Also

- hub/api-security.md
- research/owasp/authentication-failures.md
- research/authentication/password-reset-flows.md
