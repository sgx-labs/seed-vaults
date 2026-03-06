---
title: "OpenAPI Spec Quick Reference"
tags: [openapi, swagger, spec, reference, schema, yaml, entity]
content_type: entity
domain: api-design
---

# OpenAPI Spec Quick Reference

## Minimum Viable Spec

```yaml
openapi: 3.1.0
info:
  title: My API
  version: 1.0.0
paths:
  /users:
    get:
      summary: List users
      responses:
        '200':
          description: Success
```

## Common Structures

### Path with Parameters

```yaml
paths:
  /users/{id}:
    get:
      operationId: getUser
      parameters:
        - name: id
          in: path
          required: true
          schema:
            type: string
            format: uuid
      responses:
        '200':
          description: User found
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/User'
        '404':
          description: Not found
```

### Query Parameters

```yaml
parameters:
  - name: limit
    in: query
    schema:
      type: integer
      minimum: 1
      maximum: 100
      default: 20
  - name: sort
    in: query
    schema:
      type: string
      enum: [name, created_at, -name, -created_at]
```

### Request Body

```yaml
post:
  requestBody:
    required: true
    content:
      application/json:
        schema:
          $ref: '#/components/schemas/CreateUserInput'
```

### Reusable Schema

```yaml
components:
  schemas:
    User:
      type: object
      required: [id, name, email]
      properties:
        id:
          type: string
          format: uuid
          example: "usr_abc123"
        name:
          type: string
          maxLength: 100
          example: "Alice Johnson"
        email:
          type: string
          format: email
          example: "alice@example.com"
        created_at:
          type: string
          format: date-time
```

### Authentication

```yaml
components:
  securitySchemes:
    bearerAuth:
      type: http
      scheme: bearer
      bearerFormat: JWT
    apiKeyAuth:
      type: apiKey
      in: header
      name: X-API-Key

security:
  - bearerAuth: []  # Default for all endpoints
```

### Error Response (RFC 7807)

```yaml
components:
  schemas:
    ProblemDetail:
      type: object
      properties:
        type:
          type: string
          format: uri
        title:
          type: string
        status:
          type: integer
        detail:
          type: string
        instance:
          type: string
```

## Data Types

| Type | Format | Example |
|------|--------|---------|
| `string` | (none) | `"hello"` |
| `string` | `date` | `"2025-01-15"` |
| `string` | `date-time` | `"2025-01-15T10:30:00Z"` |
| `string` | `email` | `"user@example.com"` |
| `string` | `uuid` | `"550e8400-e29b-41d4-a716-446655440000"` |
| `string` | `uri` | `"https://example.com"` |
| `integer` | `int32` | `42` |
| `integer` | `int64` | `9007199254740992` |
| `number` | `float` | `3.14` |
| `boolean` | (none) | `true` |
| `array` | (none) | `[1, 2, 3]` |
| `object` | (none) | `{"key": "value"}` |

## See Also

- `research/openapi-best-practices.md` -- Best practices for writing specs
- `guides/writing-openapi-spec.md` -- Step-by-step guide
- `entities/api-design-tools.md` -- Tools that work with OpenAPI
