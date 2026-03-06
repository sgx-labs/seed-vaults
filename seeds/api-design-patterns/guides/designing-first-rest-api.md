---
title: "Designing Your First REST API"
tags: [guide, rest, beginner, design, endpoints, getting-started]
content_type: guide
domain: api-design
---

# Designing Your First REST API

## Before You Write Code

Spend 30 minutes on design before writing any code. You'll save hours of refactoring later.

### Step 1: Identify Your Resources

List the "things" your API manages. These become your URL paths.

```
Example: Task Management App
  Resources: users, projects, tasks, comments
```

### Step 2: Define the Endpoints

For each resource, map out the standard CRUD operations:

```
Users:
  GET    /users              # List all users
  POST   /users              # Create a user
  GET    /users/:id          # Get a specific user
  PATCH  /users/:id          # Update a user
  DELETE /users/:id          # Delete a user

Tasks:
  GET    /projects/:id/tasks     # List tasks in a project
  POST   /projects/:id/tasks     # Create a task in a project
  GET    /tasks/:id              # Get a specific task
  PATCH  /tasks/:id              # Update a task
  DELETE /tasks/:id              # Delete a task
```

### Step 3: Define Request/Response Shapes

Sketch out the JSON for each endpoint:

```json
// POST /users
Request:
  {"name": "Alice", "email": "alice@test.com"}

Response (201 Created):
  {
    "id": "usr_abc123",
    "name": "Alice",
    "email": "alice@test.com",
    "created_at": "2025-01-15T10:30:00Z"
  }
```

### Step 4: Define Error Responses

Pick a consistent error format and use it everywhere:

```json
{
  "type": "https://api.example.com/errors/validation-error",
  "title": "Validation Error",
  "status": 422,
  "errors": [
    {"field": "email", "message": "must be a valid email address"}
  ]
}
```

### Step 5: Plan Authentication

Choose your auth strategy before building endpoints:

- Building a web app with its own backend? -> Session tokens or JWT
- Building an API for other developers? -> API keys
- Need third-party login (Google, GitHub)? -> OAuth2

See `hub/authentication.md` for details.

### Step 6: Plan Pagination

If any endpoint returns a list, add pagination from day one:

```
GET /tasks?limit=20&cursor=abc123

Response:
{
  "data": [...],
  "has_more": true,
  "next_cursor": "def456"
}
```

Don't skip this. Adding pagination later is painful.

## Implementation Checklist

- [ ] Use plural nouns for resource URLs (`/users`, not `/user`)
- [ ] Return appropriate status codes (201 for create, 204 for delete)
- [ ] Include `Content-Type: application/json` on all responses
- [ ] Validate all input and return field-level errors
- [ ] Add pagination to all list endpoints
- [ ] Set up consistent error format
- [ ] Add CORS headers if browser clients will call your API
- [ ] Version from the start (`/v1/users`)
- [ ] Log request_id for every request

## Common First-API Mistakes

1. **Verbs in URLs** -- `/getUsers` should be `GET /users`
2. **No versioning** -- add `/v1/` from the start
3. **Inconsistent naming** -- pick camelCase or snake_case and stick with it
4. **No pagination** -- returning 10,000 results crashes the client
5. **200 for everything** -- use proper status codes
6. **No input validation** -- bad data reaches your database

## See Also

- `hub/rest-fundamentals.md` -- REST principles in depth
- `hub/error-handling.md` -- Error format design
- `hub/pagination.md` -- Pagination strategies
- `guides/writing-openapi-spec.md` -- Document your API with OpenAPI
