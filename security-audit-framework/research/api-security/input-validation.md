---
title: "Input Validation — Server-Side Patterns"
tags: [api-security, validation, sanitization, xss, injection]
content_type: research
domain: security
---

# Input Validation — Server-Side Patterns

Never trust client input. Validate on the server, sanitize for the output context, and reject anything unexpected.

## Validation Principles

1. **Validate type** — Is it a string, number, boolean?
2. **Validate length** — Minimum and maximum bounds
3. **Validate format** — Email, URL, UUID, date patterns
4. **Validate range** — Numeric min/max, date ranges
5. **Reject unexpected fields** — Strict schema, no extra properties
6. **Allowlist over denylist** — Define what IS allowed, not what is not

## Schema Validation (Node.js with Zod)

```javascript
// SECURE — Zod schema validation
const { z } = require('zod');

const createUserSchema = z.object({
  email: z.string().email().max(254),
  name: z.string().min(1).max(100).regex(/^[a-zA-Z\s'-]+$/),
  age: z.number().int().min(13).max(150).optional(),
  role: z.enum(['user', 'editor']), // Allowlist of valid values
}).strict(); // Reject additional properties

app.post('/users', (req, res) => {
  const result = createUserSchema.safeParse(req.body);
  if (!result.success) {
    return res.status(400).json({
      error: 'Validation failed',
      details: result.error.issues,
    });
  }
  // result.data is now typed and validated
  createUser(result.data);
});
```

## Schema Validation (Python with Pydantic)

```python
# SECURE — Pydantic model validation
from pydantic import BaseModel, EmailStr, Field, field_validator
import re

class CreateUserRequest(BaseModel):
    email: EmailStr
    name: str = Field(min_length=1, max_length=100)
    age: int | None = Field(default=None, ge=13, le=150)
    role: str = Field(pattern=r'^(user|editor)$')

    class Config:
        extra = 'forbid'  # Reject unexpected fields

    @field_validator('name')
    @classmethod
    def validate_name(cls, v):
        if not re.match(r"^[a-zA-Z\s'-]+$", v):
            raise ValueError('Name contains invalid characters')
        return v.strip()

@app.route('/users', methods=['POST'])
def create_user():
    try:
        data = CreateUserRequest(**request.json)
    except ValidationError as e:
        return jsonify({"error": "Validation failed", "details": e.errors()}), 400
    # data is validated and typed
```

## Output Encoding (Context-Dependent)

Validation prevents bad data from entering. Output encoding prevents bad data from executing.

| Context | Encoding | Example |
|---------|----------|---------|
| HTML body | HTML entity encoding | `&lt;script&gt;` |
| HTML attribute | Attribute encoding | `&quot;onclick=&quot;` |
| JavaScript | JavaScript encoding | `\x3cscript\x3e` |
| URL | URL encoding | `%3Cscript%3E` |
| SQL | Parameterized queries | See injection.md |

## File Path Validation

```javascript
// SECURE — Prevent path traversal
const path = require('path');

function safePath(userInput, baseDir) {
  const resolved = path.resolve(baseDir, userInput);
  if (!resolved.startsWith(path.resolve(baseDir))) {
    throw new Error('Path traversal detected');
  }
  return resolved;
}
```

## Common Mistakes

- Validating on the client only (client validation is for UX, not security)
- Using denylists (blocking `<script>` but missing `<img onerror=...>`)
- Sanitizing input once and assuming it is safe for all contexts
- Not validating file uploads (MIME type, extension, content)
- Trusting `Content-Type` headers from the client

## How to Test

1. Send requests with missing required fields
2. Send requests with extra unexpected fields
3. Send oversized strings (100KB in a name field)
4. Send special characters: `<>'";&|` in all fields
5. Send wrong types (string where number expected)

## See Also

- hub/api-security.md
- research/owasp/injection.md
- research/api-security/file-uploads.md
