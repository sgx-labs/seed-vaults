---
title: "Error Messages That Help"
tags: [errors, error-messages, user-facing, developer-facing, logging, ux]
content_type: hub
domain: technical-writing
---

# Error Messages That Help

## Three Audiences, Three Styles

Every error message has an audience. Write differently for each:

1. **End users** -- non-technical people who need to fix something or know it's being handled
2. **Developers** -- people integrating your API who need to debug their request
3. **Operators** -- your team, reading logs at 3 AM during an incident

## User-Facing Errors

**Don't write this:**

```
Error: ECONNREFUSED 127.0.0.1:5432
NullPointerException at UserService.java:247
Request failed with status 500
```

**Write this instead:**

```
We couldn't save your changes. Please try again in a few moments.
If this keeps happening, contact support with reference ID: req_abc123.
```

**Principles:**
- Say what happened in plain language
- Say what the user can do about it
- Never expose stack traces, internal IPs, or database errors
- Include a reference ID for support to look up the details

## Developer-Facing Errors (API)

**Don't write this:**

```json
{"error": true, "message": "bad request"}
```

**Write this instead:**

```json
{
  "type": "https://api.example.com/errors/validation",
  "title": "Validation Error",
  "status": 422,
  "detail": "The 'email' field must be a valid email address.",
  "errors": [
    {
      "field": "email",
      "value": "not-an-email",
      "message": "Must be a valid email address (e.g., user@example.com)"
    }
  ]
}
```

**Principles:**
- Use a consistent error format (RFC 7807 Problem Details is a good standard)
- Name the specific field and value that caused the error
- Show what a correct value looks like
- Return appropriate HTTP status codes -- don't hide 422s behind 400 or 200
- Include a machine-readable error type for programmatic handling

## Log Messages (Operator-Facing)

**Don't write this:**

```
ERROR: something went wrong
ERROR: failed
ERROR: null
```

**Write this instead:**

```
ERROR payment_service: charge failed | user_id=usr_123 amount=4999
  reason="card_declined" provider="stripe" stripe_error="insufficient_funds"
  request_id=req_abc123 duration_ms=234
```

**Principles:**
- Structured format (key=value or JSON) for searchability
- Include enough context to reproduce: who, what, why, and identifiers
- Use consistent field names across all services
- Include request IDs that correlate across services
- Don't log sensitive data (passwords, tokens, full card numbers)

## The Error Message Checklist

For every error message, ask:

1. **Does it say what happened?** Not "Error" but "Your session has expired."
2. **Does it say why?** Not "Invalid request" but "The email field is required."
3. **Does it say what to do?** Not "Contact support" but "Please sign in again."
4. **Is it appropriate for the audience?** Users get plain English. Developers get field names. Logs get structured data.
5. **Does it avoid blame?** "We couldn't process your request" not "You sent an invalid request."

## Tone

- **Be direct, not apologetic.** "Your password must be at least 8 characters" not "We're sorry, but unfortunately the password you entered doesn't meet our requirements."
- **Be specific, not vague.** "File must be under 10MB (yours is 24MB)" not "File too large."
- **Be helpful, not condescending.** Suggest the fix without over-explaining obvious things.

## See Also

- `hub/api-documentation.md` -- Documenting error responses in API docs
- `hub/runbooks-and-playbooks.md` -- Errors that link to runbooks
- `hub/code-comments-philosophy.md` -- Comments explaining error handling logic
- `hub/writing-style-guide.md` -- Tone and clarity in all writing
