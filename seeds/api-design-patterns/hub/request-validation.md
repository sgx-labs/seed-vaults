---
title: "Request Validation"
tags: [validation, input, sanitization, schema, security, request-body]
content_type: hub
domain: api-design
---

# Request Validation

## Overview

Every piece of data that enters your API is potentially malicious, malformed, or just wrong. Request validation ensures that your handlers only process clean, expected input. Validate early, validate strictly, and return helpful errors.

## What to Validate

### 1. Request Body

Validate structure, types, required fields, and constraints.

```json
// Expected
{
  "name": "Alice",           // string, 1-100 chars, required
  "email": "alice@test.com", // valid email format, required
  "age": 25                  // integer, 0-150, optional
}

// Reject
{
  "name": "",                // empty string
  "email": "not-an-email",   // invalid format
  "age": -5                  // out of range
}
```

### 2. Path Parameters

Validate types and formats.

```
GET /users/123       -> valid (integer ID)
GET /users/abc       -> 400 (expected integer)
GET /users/0         -> 400 (ID must be positive)
```

### 3. Query Parameters

Validate types, ranges, and allowed values.

```
GET /users?limit=10&sort=name     -> valid
GET /users?limit=-1               -> 400 (must be positive)
GET /users?limit=10000            -> 400 (max 100)
GET /users?sort=password          -> 400 (not an allowed sort field)
```

### 4. Headers

Validate required headers, content types, and auth tokens.

```
POST /users
Content-Type: text/plain    -> 415 Unsupported Media Type
                               (expected application/json)
```

## Validation Strategies

### Schema-Based Validation

Define a schema and validate against it automatically. Most frameworks support this.

```
Schema:
  name:
    type: string
    required: true
    minLength: 1
    maxLength: 100
  email:
    type: string
    required: true
    format: email
  role:
    type: string
    enum: [admin, editor, viewer]
```

Benefits: declarative, reusable, can generate documentation, catches entire classes of bugs.

### Validation Middleware

Validate before the request reaches your handler. The handler can trust its input.

```
router.post("/users",
  validate(createUserSchema),   // <- rejects bad input with 422
  createUserHandler              // <- only sees valid data
)
```

### Defense in Depth

Validate at multiple layers:
1. **API gateway** -- block obviously malformed requests
2. **Middleware** -- schema validation
3. **Handler** -- business rule validation
4. **Database** -- constraints as last line of defense

Don't rely on any single layer. Each catches different classes of errors.

## Input Sanitization

Validation checks format. Sanitization cleans content.

- **Trim whitespace** from string inputs
- **Normalize unicode** to prevent homograph attacks
- **Strip HTML tags** if you don't expect HTML
- **Limit string lengths** to prevent memory abuse
- **Reject null bytes** in strings

Sanitize AFTER validation, not instead of it.

## Error Responses for Validation Failures

Return field-level errors so clients can highlight specific form fields:

```json
{
  "type": "https://api.example.com/errors/validation-error",
  "title": "Validation Error",
  "status": 422,
  "errors": [
    { "field": "email", "code": "INVALID_FORMAT", "message": "Must be a valid email" },
    { "field": "name", "code": "REQUIRED", "message": "Name is required" }
  ]
}
```

Return ALL errors, not just the first one. Clients shouldn't have to submit repeatedly to discover each problem.

## Common Mistakes

- **Trusting client input** -- never skip server-side validation because "the frontend validates"
- **Returning only the first error** -- return all validation errors at once
- **Inconsistent validation** -- same field validated differently across endpoints
- **No max length on strings** -- a 10MB name field will ruin your day
- **Validating too late** -- catch bad input before it touches business logic

## See Also

- `hub/error-handling.md` -- Error format for validation responses
- `research/api-security-checklist.md` -- Security implications of input validation
- `hub/rest-fundamentals.md` -- HTTP status codes for validation errors
