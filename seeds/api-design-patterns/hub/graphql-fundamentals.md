---
title: "GraphQL Fundamentals"
tags: [graphql, schema, resolvers, queries, mutations, subscriptions, n-plus-one]
content_type: hub
domain: api-design
---

# GraphQL Fundamentals

## Overview

GraphQL is a query language for APIs that lets clients request exactly the data they need. Instead of multiple endpoints returning fixed shapes, a single endpoint accepts queries that specify the exact fields, nested relationships, and data transformations desired. Developed by Facebook, now widely adopted.

## Core Concepts

### Schema
The type system that defines your API's capabilities.

```graphql
type User {
  id: ID!
  name: String!
  email: String!
  posts: [Post!]!
}

type Post {
  id: ID!
  title: String!
  body: String!
  author: User!
  createdAt: DateTime!
}

type Query {
  user(id: ID!): User
  users(limit: Int, offset: Int): [User!]!
  post(id: ID!): Post
}

type Mutation {
  createUser(input: CreateUserInput!): User!
  updatePost(id: ID!, input: UpdatePostInput!): Post!
  deletePost(id: ID!): Boolean!
}
```

### Queries
Clients request exactly what they need.

```graphql
query {
  user(id: "123") {
    name
    email
    posts {
      title
      createdAt
    }
  }
}
```

Response mirrors the query shape:
```json
{
  "data": {
    "user": {
      "name": "Alice",
      "email": "alice@test.com",
      "posts": [
        { "title": "First Post", "createdAt": "2025-01-15" }
      ]
    }
  }
}
```

### Mutations
All writes go through mutations. Always return the modified object.

```graphql
mutation {
  createUser(input: { name: "Bob", email: "bob@test.com" }) {
    id
    name
  }
}
```

### Subscriptions
Real-time data via WebSocket.

```graphql
subscription {
  postCreated {
    id
    title
    author { name }
  }
}
```

## The N+1 Problem

The biggest GraphQL performance pitfall. When resolving nested fields, a naive implementation makes one database query per parent.

```
Query: users { posts { title } }

Without batching:
  SELECT * FROM users          -- 1 query
  SELECT * FROM posts WHERE user_id = 1  -- N queries
  SELECT * FROM posts WHERE user_id = 2  -- (one per user)
  ...
```

**Solution: DataLoader pattern.** Batch and deduplicate database requests.

```
With DataLoader:
  SELECT * FROM users                              -- 1 query
  SELECT * FROM posts WHERE user_id IN (1, 2, 3)  -- 1 query (batched)
```

Every GraphQL server should use a DataLoader or equivalent. This is non-negotiable for production.

## When to Use GraphQL

**Good fit**: Mobile apps (minimize over-fetching on limited bandwidth), complex data relationships, clients that need flexibility in what they fetch, APIs consumed by multiple frontends with different data needs.

**Poor fit**: Simple CRUD APIs, file uploads (awkward in GraphQL), real-time streaming (subscriptions exist but are complex), public APIs for external developers (REST is more widely understood), server-to-server communication.

## When NOT to Use GraphQL

- **You have a simple API** -- 10-15 endpoints with straightforward CRUD. REST is simpler.
- **Your team doesn't know GraphQL** -- the learning curve is real. Schema design, resolver patterns, DataLoader, caching strategies.
- **You need HTTP caching** -- GraphQL POST requests don't cache at the HTTP level. You need client-side or CDN-level solutions.
- **Your API is mostly writes** -- GraphQL shines for reads. Mutations are just function calls.

## Best Practices

1. **Use input types for mutations** -- don't pass individual arguments
2. **Return the modified object from mutations** -- clients can update their cache
3. **Use DataLoader** -- always. No exceptions.
4. **Implement query complexity limits** -- prevent clients from requesting deeply nested queries that crash your server
5. **Use persisted queries** -- whitelist allowed queries in production to prevent abuse
6. **Paginate with connections** -- use the Relay connection spec (edges, nodes, pageInfo)

## See Also

- `entities/graphql-vs-rest.md` -- Side-by-side comparison
- `research/event-driven-apis.md` -- GraphQL subscriptions vs other real-time options
- `decisions/rest-vs-graphql-decision.md` -- Decision template
- `guides/migrating-rest-to-graphql.md` -- Migration guide
