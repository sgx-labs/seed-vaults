---
title: "A10:2025 — Mishandling of Exceptional Conditions"
tags: [owasp, error-handling, exceptions, fail-secure]
content_type: research
domain: security
---

# A10:2025 — Mishandling of Exceptional Conditions

NEW in OWASP Top 10 2025. Applications that do not properly handle errors and exceptions can leak information, bypass security controls, or enter insecure states.

## Common Failures

- Stack traces exposed to users
- Security controls bypassed when exceptions occur
- Catch-all handlers that swallow critical errors
- Resource exhaustion from unhandled edge cases
- Fail-open behavior (error = allow access)

## Vulnerable: Stack Trace Exposure (Express.js) — DO NOT USE

```javascript
// VULNERABLE — Default Express error handler leaks stack traces
app.get('/users/:id', async (req, res) => {
  const user = await db.getUser(req.params.id);
  res.json(user); // If db throws, Express sends stack trace to client
});
```

## Secure: Custom Error Handler

```javascript
// SECURE — Generic errors to client, detailed errors to logs
app.get('/users/:id', async (req, res, next) => {
  try {
    const user = await db.getUser(req.params.id);
    if (!user) return res.status(404).json({ error: 'Not found' });
    res.json(user);
  } catch (err) {
    next(err); // Pass to error handler
  }
});

// Global error handler — MUST be last middleware
app.use((err, req, res, next) => {
  logger.error('unhandled_error', {
    error: err.message,
    stack: err.stack,
    path: req.path,
    method: req.method,
  });
  res.status(500).json({ error: 'Internal server error' });
});
```

## Vulnerable: Fail-Open Auth (Python) — DO NOT USE

```python
# VULNERABLE — Exception causes access to be granted
def check_authorization(user, resource):
    try:
        permissions = auth_service.get_permissions(user.id)
        return resource.id in permissions.allowed_resources
    except Exception:
        return True  # Fail-open: if auth service is down, allow access
```

## Secure: Fail-Closed Auth

```python
# SECURE — Exception causes access to be denied
def check_authorization(user, resource):
    try:
        permissions = auth_service.get_permissions(user.id)
        return resource.id in permissions.allowed_resources
    except Exception as e:
        logger.error('auth.permission_check_failed',
                     user_id=user.id,
                     resource_id=resource.id,
                     error=str(e))
        return False  # Fail-closed: deny access on any error
```

## Vulnerable: Swallowed Errors (Go) — DO NOT USE

```go
// VULNERABLE — Error ignored, processing continues with nil/zero values
user, _ := db.GetUser(userID)
// user could be nil, causing panic or incorrect behavior
processUser(user)
```

## Secure: Explicit Error Handling (Go)

```go
// SECURE — Every error checked and handled
user, err := db.GetUser(userID)
if err != nil {
    logger.Error("failed to get user",
        "user_id", userID,
        "error", err)
    return fmt.Errorf("failed to get user: %w", err)
}
if user == nil {
    return ErrUserNotFound
}
processUser(user)
```

## Principles

1. **Fail closed** — Errors should deny access, not grant it
2. **Log internally, not externally** — Users get generic messages; logs get detail
3. **Handle every error** — Especially in Go, check every error return
4. **Do not catch and ignore** — If you catch, log and respond appropriately
5. **Resource cleanup** — Use defer/finally/context managers to prevent leaks

## How to Test

1. Send malformed requests and check responses for stack traces
2. Simulate auth service failure and verify access is denied (fail-closed)
3. Search for empty catch blocks or ignored error returns
4. Check that error responses do not leak internal paths or versions
5. Test resource exhaustion (large payloads, many connections)

## See Also

- hub/owasp-top-10.md
- research/owasp/security-misconfiguration.md
- research/owasp/logging-monitoring-failures.md
