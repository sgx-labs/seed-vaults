---
title: "GraphQL vs REST Comparison"
tags: [graphql, rest, comparison, tradeoffs, reference, entity]
content_type: entity
domain: api-design
---

# GraphQL vs REST Comparison

## Side-by-Side

| Factor | REST | GraphQL |
|--------|------|---------|
| **Endpoints** | Many (one per resource) | One (single /graphql endpoint) |
| **Data fetching** | Server decides what to return | Client decides what to return |
| **Over-fetching** | Common (fixed response shape) | Eliminated (exact fields requested) |
| **Under-fetching** | Common (multiple requests needed) | Eliminated (nested queries) |
| **Caching** | HTTP caching built-in (GET, ETags) | Complex (POST requests, client-side) |
| **Error handling** | HTTP status codes | Always 200, errors in response body |
| **File uploads** | Native (multipart) | Awkward (separate spec or REST fallback) |
| **Real-time** | SSE or WebSocket (separate) | Subscriptions (built-in concept) |
| **Versioning** | URL or header versioning | Schema evolution (deprecate fields) |
| **Tooling** | Universal (curl, any HTTP client) | Specialized (GraphQL clients, playground) |
| **Learning curve** | Low | Medium-High |
| **Type system** | Optional (OpenAPI) | Built-in (schema) |
| **N+1 problem** | Handled at DB/ORM level | Requires DataLoader pattern |
| **API discovery** | Documentation + OpenAPI | Introspection (self-documenting) |
| **Browser DevTools** | Easy to inspect | POST body inspection needed |

## When REST Wins

- **Simple CRUD APIs** -- REST maps directly to database operations
- **Public APIs** -- more developers know REST, lower barrier to entry
- **Caching matters** -- HTTP caching works out of the box
- **File handling** -- uploads and downloads are native
- **Server-to-server** -- simpler, less overhead
- **Small team** -- less infrastructure and tooling to maintain

## When GraphQL Wins

- **Multiple frontends** -- mobile app needs minimal data, web needs full data
- **Complex data relationships** -- deep nesting without multiple round trips
- **Rapid frontend iteration** -- frontend can change queries without backend changes
- **Bandwidth-constrained clients** -- mobile apps fetching exactly what they need
- **Strong typing matters** -- schema-first development with code generation

## When Neither is Clearly Better

- **Microservices** -- gRPC often outperforms both for internal communication
- **Real-time heavy** -- WebSocket/SSE may be simpler than GraphQL subscriptions
- **Mixed workloads** -- some endpoints suit REST, some suit GraphQL (hybrid is valid)

## The Hybrid Approach

Many organizations use both:
- REST for simple CRUD, file operations, and public APIs
- GraphQL for complex data fetching, dashboard aggregation, and frontend-driven queries
- gRPC for internal service-to-service communication

This is pragmatic, not indecisive. Use the right tool for each problem.

## See Also

- `hub/rest-fundamentals.md` -- REST design in depth
- `hub/graphql-fundamentals.md` -- GraphQL design in depth
- `hub/grpc-fundamentals.md` -- gRPC as a third option
- `decisions/rest-vs-graphql-decision.md` -- Decision template
