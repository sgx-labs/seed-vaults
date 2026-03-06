---
title: "Migrating from REST to GraphQL"
tags: [guide, graphql, rest, migration, incremental, schema]
content_type: guide
domain: api-design
---

# Migrating from REST to GraphQL

## Overview

You have a REST API and want to add GraphQL. This guide covers an incremental migration -- running both side by side, not a risky big-bang switch.

## Step 1: Decide What to Migrate

Don't migrate everything. GraphQL is best for:
- Endpoints where clients over-fetch (fetching full objects when they need 3 fields)
- Endpoints where clients under-fetch (making 5 requests to build one view)
- Complex nested data (user -> orders -> items -> reviews)

Keep REST for:
- File uploads and downloads
- Simple CRUD that doesn't benefit from GraphQL
- Webhooks and callbacks
- Server-to-server communication

## Step 2: Design the Schema

Map your REST resources to GraphQL types:

```
REST:
  GET /users/:id        -> User object
  GET /users/:id/orders -> list of Order objects
  GET /orders/:id       -> Order object with items

GraphQL:
  type User {
    id: ID!
    name: String!
    email: String!
    orders(limit: Int, cursor: String): OrderConnection!
  }

  type Order {
    id: ID!
    total: Int!
    status: OrderStatus!
    items: [OrderItem!]!
    user: User!
  }
```

Rules:
- Start with read operations (queries). Add mutations later.
- Use your existing data models -- don't redesign the domain.
- Add connections (Relay spec) for paginated lists from day one.

## Step 3: Implement Resolvers that Call REST

The fastest path: GraphQL resolvers call your existing REST endpoints internally.

```
// GraphQL resolver calls existing REST handler or service layer
resolvers:
  Query:
    user(id):
      return userService.getById(id)    // Same service your REST handler uses

  User:
    orders(user, {limit, cursor}):
      return orderService.getByUser(user.id, limit, cursor)
```

This lets you ship GraphQL without rewriting any business logic. Your REST API continues working unchanged.

## Step 4: Add DataLoader

Before going live, add DataLoader to prevent N+1 queries:

```
// Without DataLoader: N+1
query { users { orders { items } } }
  -> 1 query for users
  -> N queries for orders (one per user)
  -> N*M queries for items (one per order)

// With DataLoader: batched
  -> 1 query for users
  -> 1 query for all orders (batched by user IDs)
  -> 1 query for all items (batched by order IDs)
```

This is non-negotiable for production. Test with realistic data volumes.

## Step 5: Run Both Side by Side

```
/api/v1/users          -> REST (existing)
/api/v1/users/:id      -> REST (existing)
/graphql               -> GraphQL (new)
```

- Share authentication middleware between REST and GraphQL
- Share rate limiting (count GraphQL queries as requests)
- Share logging and monitoring

## Step 6: Migrate Clients Incrementally

Pick one frontend view that benefits most from GraphQL. Migrate that view. Measure the improvement (fewer requests, less data transferred, faster render). Use that data to justify migrating more.

Don't force all clients to switch at once. Some views are better served by REST.

## Step 7: Secure GraphQL

GraphQL introduces new attack surfaces:

- **Query depth limiting** -- prevent deeply nested queries that crash your server
- **Query complexity analysis** -- assign costs to fields, reject queries over a budget
- **Persisted queries** -- in production, only allow pre-approved queries (prevents abuse)
- **Introspection** -- disable in production (prevents schema discovery by attackers)
- **Rate limiting** -- limit by query complexity, not just request count

## When to Stop

You don't need to migrate everything. A healthy endpoint:
- REST for simple CRUD, file operations, webhooks, public API
- GraphQL for complex reads, dashboard aggregation, mobile clients

This is the hybrid approach. It's pragmatic, not incomplete.

## See Also

- `hub/graphql-fundamentals.md` -- GraphQL design principles
- `entities/graphql-vs-rest.md` -- When each approach is better
- `decisions/rest-vs-graphql-decision.md` -- Decision template
