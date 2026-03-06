---
title: "OpenAPI/Swagger Best Practices"
tags: [openapi, swagger, documentation, spec, schema, api-docs, code-generation]
content_type: research
domain: api-design
---

# OpenAPI/Swagger Best Practices

## The Core Idea

OpenAPI (formerly Swagger) is the standard way to describe REST APIs. A well-written OpenAPI spec serves as documentation, enables code generation, powers testing tools, and acts as a contract between frontend and backend teams.

## Spec Structure

```yaml
openapi: 3.1.0
info:
  title: My API
  version: 1.0.0
  description: |
    API for managing users and orders.

servers:
  - url: https://api.example.com/v1
    description: Production
  - url: https://staging-api.example.com/v1
    description: Staging

paths:
  /users:
    get:
      summary: List users
      operationId: listUsers
      tags: [users]
      parameters:
        - name: limit
          in: query
          schema:
            type: integer
            minimum: 1
            maximum: 100
            default: 20
      responses:
        '200':
          description: List of users
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/UserList'

components:
  schemas:
    User:
      type: object
      required: [id, name, email]
      properties:
        id:
          type: string
          format: uuid
        name:
          type: string
          maxLength: 100
        email:
          type: string
          format: email
```

## Best Practices

### 1. Use operationId on Every Endpoint

```yaml
paths:
  /users:
    get:
      operationId: listUsers    # Used by code generators as function name
    post:
      operationId: createUser
  /users/{id}:
    get:
      operationId: getUser
```

Code generators use operationId for method names. Without it, you get auto-generated names like `getApiV1Users` that nobody wants.

### 2. Use $ref for Reusable Schemas

Don't inline schemas. Define them in `components/schemas` and reference them.

```yaml
# Bad: inline everywhere
responses:
  '200':
    content:
      application/json:
        schema:
          type: object
          properties:
            id: { type: string }
            name: { type: string }

# Good: reference shared schema
responses:
  '200':
    content:
      application/json:
        schema:
          $ref: '#/components/schemas/User'
```

### 3. Document Error Responses

Every endpoint should document its error cases.

```yaml
responses:
  '200':
    description: Success
    content:
      application/json:
        schema:
          $ref: '#/components/schemas/User'
  '400':
    description: Invalid request
    content:
      application/json:
        schema:
          $ref: '#/components/schemas/ProblemDetail'
  '404':
    description: User not found
  '422':
    description: Validation error
    content:
      application/json:
        schema:
          $ref: '#/components/schemas/ValidationError'
```

### 4. Use Tags to Organize Endpoints

```yaml
tags:
  - name: users
    description: User management
  - name: orders
    description: Order processing

paths:
  /users:
    get:
      tags: [users]
  /orders:
    get:
      tags: [orders]
```

### 5. Provide Examples

```yaml
components:
  schemas:
    User:
      type: object
      properties:
        id:
          type: string
          example: "usr_abc123"
        name:
          type: string
          example: "Alice Johnson"
        email:
          type: string
          example: "alice@example.com"
```

### 6. Use Enums for Fixed Values

```yaml
properties:
  status:
    type: string
    enum: [active, inactive, suspended]
    description: "Account status"
  role:
    type: string
    enum: [admin, editor, viewer]
```

## Spec-First vs Code-First

**Spec-first**: Write the OpenAPI spec, then generate server stubs and client SDKs.
- Pros: API design happens before implementation, serves as a contract
- Cons: Spec can drift from implementation if not validated

**Code-first**: Write the code, generate the spec from annotations/decorators.
- Pros: Spec always matches implementation, less duplicated effort
- Cons: Design happens in code, harder to review API design separately

**Recommendation**: Spec-first for public APIs and team collaboration. Code-first for internal APIs and rapid development. Either way, validate that the spec matches reality.

## Tools

| Tool | Purpose |
|------|---------|
| Swagger Editor | Write and preview specs |
| Redoc | Beautiful API documentation from spec |
| Stoplight | Visual API design |
| openapi-generator | Generate client/server code |
| Prism | Mock server from spec |
| Spectral | Lint your OpenAPI spec |

## See Also

- `entities/openapi-quick-reference.md` -- Quick reference for OpenAPI 3.x
- `guides/writing-openapi-spec.md` -- Step-by-step guide
- `hub/error-handling.md` -- Error schemas for OpenAPI
