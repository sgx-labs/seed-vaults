---
title: "REST Fundamentals"
tags: [rest, http, resources, methods, status-codes, constraints, api-design]
content_type: hub
domain: api-design
---

# REST Fundamentals

## Overview

REST (Representational State Transfer) is an architectural style for designing networked applications. It uses HTTP as its protocol, resources as its core abstraction, and standard methods for operations. Most public APIs are REST or REST-like. Understanding REST constraints helps you design APIs that are predictable, scalable, and easy to consume.

## Resource Naming

Resources are nouns, not verbs. URLs identify things, not actions.

```
Good:
  GET    /users              # list users
  GET    /users/123          # get user 123
  POST   /users              # create a user
  PUT    /users/123          # replace user 123
  PATCH  /users/123          # partial update user 123
  DELETE /users/123          # delete user 123

Bad:
  GET    /getUsers
  POST   /createUser
  POST   /deleteUser/123
```

**Nested resources** express relationships:
```
GET  /users/123/orders          # orders belonging to user 123
GET  /users/123/orders/456      # specific order for user 123
```

Keep nesting shallow -- two levels max. Beyond that, use query parameters or top-level resources with filters.

**Plurals vs singulars**: Use plural nouns consistently. `/users`, not `/user`. The exception is singleton resources: `/users/123/profile`.

## HTTP Methods

| Method | Purpose | Idempotent | Safe | Request Body |
|--------|---------|-----------|------|-------------|
| GET | Read a resource | Yes | Yes | No |
| POST | Create a resource | No | No | Yes |
| PUT | Replace a resource entirely | Yes | No | Yes |
| PATCH | Partial update | No* | No | Yes |
| DELETE | Remove a resource | Yes | No | Optional |
| HEAD | Get headers only (no body) | Yes | Yes | No |
| OPTIONS | Get allowed methods | Yes | Yes | No |

*PATCH can be made idempotent with careful design, but the spec does not require it.

**PUT vs PATCH**: PUT replaces the entire resource -- omitted fields get reset. PATCH only updates the fields you send. Most APIs should support PATCH for updates; PUT is useful when clients always send complete objects.

## Status Codes

Use status codes honestly. Don't return 200 for everything.

| Range | Meaning | Common Codes |
|-------|---------|-------------|
| 2xx | Success | 200 OK, 201 Created, 204 No Content |
| 3xx | Redirection | 301 Moved, 304 Not Modified |
| 4xx | Client error | 400 Bad Request, 401 Unauthorized, 403 Forbidden, 404 Not Found, 409 Conflict, 422 Unprocessable, 429 Too Many Requests |
| 5xx | Server error | 500 Internal, 502 Bad Gateway, 503 Unavailable, 504 Timeout |

See `entities/http-status-codes.md` for the complete reference.

## REST Constraints

The six REST constraints (only the first five are required):

1. **Client-Server** -- separate concerns between UI and data storage
2. **Stateless** -- each request contains all information needed to process it
3. **Cacheable** -- responses must declare themselves cacheable or not
4. **Uniform Interface** -- consistent resource identification, manipulation through representations, self-descriptive messages, HATEOAS
5. **Layered System** -- client can't tell if it's connected directly to the server
6. **Code on Demand** (optional) -- server can extend client by sending executable code

Most APIs satisfy constraints 1-3 and partially satisfy 4. Very few implement HATEOAS. That's okay -- pragmatism over purity.

## When to Use REST

**Good fit**: CRUD operations, public APIs, simple resource models, browser clients, mobile apps, any API that needs to be widely consumed.

**Poor fit**: Real-time data (consider WebSocket/SSE), complex queries with many joins (consider GraphQL), high-performance internal services (consider gRPC), event-driven systems (consider message queues).

## Research Notes

- `research/api-versioning-strategies.md` -- Versioning strategies for REST APIs
- `research/event-driven-apis.md` -- Alternatives to REST for real-time data

## See Also

- `hub/error-handling.md` -- Consistent error responses
- `hub/pagination.md` -- Paginating REST collections
- `hub/versioning.md` -- Evolving your REST API
- `hub/caching.md` -- HTTP caching for REST
- `entities/http-status-codes.md` -- Complete status code reference
- `entities/rest-maturity-model.md` -- Richardson Maturity Model
