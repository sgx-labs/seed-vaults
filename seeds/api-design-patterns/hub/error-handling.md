---
title: "Error Handling"
tags: [errors, error-handling, rfc-7807, problem-details, status-codes, error-codes]
content_type: hub
domain: api-design
---

# Error Handling

## Overview

Consistent error responses are one of the most important aspects of API design. When something goes wrong, clients need enough information to understand what happened, why, and how to fix it. RFC 7807 Problem Details provides a standard format. Even if you don't adopt it fully, its principles apply.

## RFC 7807 Problem Details

The standard format for HTTP API error responses.

```json
{
  "type": "https://api.example.com/errors/insufficient-funds",
  "title": "Insufficient Funds",
  "status": 422,
  "detail": "Your account balance of $10.00 is not enough for a $25.00 transfer.",
  "instance": "/transfers/abc123"
}
```

| Field | Required | Purpose |
|-------|----------|---------|
| `type` | Yes | URI identifying the error type (documentation link) |
| `title` | Yes | Short human-readable summary |
| `status` | Yes | HTTP status code |
| `detail` | No | Human-readable explanation specific to this occurrence |
| `instance` | No | URI identifying this specific error occurrence |

You can add custom fields:

```json
{
  "type": "https://api.example.com/errors/validation-error",
  "title": "Validation Error",
  "status": 422,
  "detail": "2 fields failed validation.",
  "errors": [
    { "field": "email", "message": "must be a valid email address" },
    { "field": "age", "message": "must be at least 18" }
  ]
}
```

## Error Design Principles

### 1. Use Appropriate Status Codes

Match the status code to the actual problem. Don't return 200 with an error body. Don't return 500 for client mistakes.

```
400 -> malformed request (bad JSON, wrong content type)
401 -> not authenticated (missing or invalid credentials)
403 -> not authorized (authenticated but lacks permission)
404 -> resource doesn't exist
409 -> conflict (duplicate, concurrent modification)
422 -> valid request but semantically wrong (validation errors)
429 -> rate limited
500 -> unexpected server error (your fault, not theirs)
```

### 2. Be Specific but Safe

Tell clients what went wrong, but don't leak internal details.

```
Good:  "User with email 'bob@test.com' already exists"
Bad:   "UNIQUE constraint failed: users.email (SQLite3::ConstraintException)"
```

### 3. Use Error Codes

Supplement HTTP status codes with machine-readable error codes. This lets clients handle specific errors programmatically.

```json
{
  "type": "https://api.example.com/errors/duplicate-email",
  "title": "Duplicate Email",
  "status": 409,
  "code": "DUPLICATE_EMAIL",
  "detail": "A user with this email already exists."
}
```

Error codes should be stable. Clients will switch on them. Document every code.

### 4. Validation Errors Need Field-Level Detail

For 422 responses, list every field that failed validation:

```json
{
  "type": "https://api.example.com/errors/validation-error",
  "title": "Validation Error",
  "status": 422,
  "errors": [
    { "field": "email", "code": "INVALID_FORMAT", "message": "Must be a valid email" },
    { "field": "password", "code": "TOO_SHORT", "message": "Must be at least 8 characters" }
  ]
}
```

### 5. Consistent Format Everywhere

Every error from every endpoint should use the same format. Wrap framework errors, catch unhandled exceptions, and normalize third-party errors. Nothing is worse than inconsistent error shapes.

## Common Mistakes

- **Returning 200 for errors** -- some legacy APIs do this. Don't.
- **Different error formats per endpoint** -- use middleware to enforce consistency.
- **Leaking stack traces in production** -- serialize them to logs, not responses.
- **Generic "An error occurred" messages** -- tell the client what to fix.
- **Not including a request ID** -- makes debugging impossible. Add a `request_id` field.

## See Also

- `entities/http-status-codes.md` -- Complete status code reference
- `hub/request-validation.md` -- Validation that generates good error messages
- `hub/rest-fundamentals.md` -- Status codes in REST context
