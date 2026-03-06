---
title: "Writing an OpenAPI Spec"
tags: [guide, openapi, swagger, documentation, spec, yaml]
content_type: guide
domain: api-design
---

# Writing an OpenAPI Spec

## Overview

This guide walks through creating an OpenAPI 3.1 spec from scratch. By the end, you'll have a spec that generates documentation, enables code generation, and powers contract testing.

## Step 1: Start with the Skeleton

```yaml
openapi: 3.1.0
info:
  title: Task Manager API
  version: 1.0.0
  description: |
    API for managing tasks and projects.

    ## Authentication
    All endpoints require a Bearer token in the Authorization header.
  contact:
    name: API Support
    email: api@example.com

servers:
  - url: https://api.example.com/v1
    description: Production
  - url: https://staging-api.example.com/v1
    description: Staging
  - url: http://localhost:3000/v1
    description: Local development

paths: {}
components:
  schemas: {}
```

## Step 2: Define Your Schemas First

Define reusable data models before writing paths. This keeps your spec DRY.

```yaml
components:
  schemas:
    Task:
      type: object
      required: [id, title, status, created_at]
      properties:
        id:
          type: string
          format: uuid
          example: "tsk_abc123"
        title:
          type: string
          maxLength: 200
          example: "Implement user authentication"
        description:
          type: string
          maxLength: 5000
          example: "Add JWT-based auth to all protected endpoints"
        status:
          type: string
          enum: [todo, in_progress, done]
          example: "in_progress"
        created_at:
          type: string
          format: date-time
          example: "2025-01-15T10:30:00Z"

    CreateTaskInput:
      type: object
      required: [title]
      properties:
        title:
          type: string
          maxLength: 200
        description:
          type: string
          maxLength: 5000
        status:
          type: string
          enum: [todo, in_progress, done]
          default: "todo"

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
```

## Step 3: Add Security Scheme

```yaml
components:
  securitySchemes:
    bearerAuth:
      type: http
      scheme: bearer
      bearerFormat: JWT
      description: "JWT access token"

security:
  - bearerAuth: []
```

The top-level `security` applies to all endpoints. Override per-endpoint for public routes.

## Step 4: Write Your Paths

```yaml
paths:
  /tasks:
    get:
      operationId: listTasks
      summary: List tasks
      tags: [tasks]
      parameters:
        - name: status
          in: query
          schema:
            type: string
            enum: [todo, in_progress, done]
        - name: limit
          in: query
          schema:
            type: integer
            minimum: 1
            maximum: 100
            default: 20
        - name: cursor
          in: query
          schema:
            type: string
      responses:
        '200':
          description: List of tasks
          content:
            application/json:
              schema:
                type: object
                properties:
                  data:
                    type: array
                    items:
                      $ref: '#/components/schemas/Task'
                  pagination:
                    type: object
                    properties:
                      next_cursor:
                        type: string
                        nullable: true
                      has_more:
                        type: boolean
        '401':
          description: Not authenticated
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/ProblemDetail'

    post:
      operationId: createTask
      summary: Create a task
      tags: [tasks]
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/CreateTaskInput'
      responses:
        '201':
          description: Task created
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/Task'
        '422':
          description: Validation error
```

## Step 5: Validate Your Spec

Use a linter to catch mistakes:

```bash
# Install Spectral
npm install -g @stoplight/spectral-cli

# Lint your spec
spectral lint openapi.yaml
```

Common issues Spectral catches:
- Missing operationId
- Missing response descriptions
- Unused schemas
- Invalid references

## Step 6: Generate Documentation

```bash
# Redoc (static HTML)
npx @redocly/cli build-docs openapi.yaml --output docs.html

# Swagger UI (interactive)
# Serve the spec with swagger-ui-express or similar
```

## Tips

- **Use operationId everywhere** -- code generators use it for method names
- **Add examples** -- they appear in docs and mock servers
- **Document all error responses** -- not just 200
- **Use tags** -- they group endpoints in the documentation
- **Keep schemas in components** -- never inline complex schemas

## See Also

- `entities/openapi-quick-reference.md` -- Quick reference for OpenAPI syntax
- `research/openapi-best-practices.md` -- Advanced OpenAPI patterns
- `entities/api-design-tools.md` -- Tools that work with OpenAPI specs
