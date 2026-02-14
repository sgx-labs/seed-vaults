---
title: "GraphQL Security Patterns"
tags: [api-security, graphql, query-complexity, introspection, depth-limiting, batching, field-authorization]
content_type: research
domain: security
---

# GraphQL Security Patterns

GraphQL gives clients powerful query capabilities, which makes it a unique attack surface. Default configurations are often insecure.

## Key Risks

| Risk | Description | Impact |
|------|-------------|--------|
| Introspection in production | Schema exposed to attackers | Information disclosure |
| Unbounded queries | Deeply nested or wide queries | Denial of service |
| Authorization bypass | Field-level auth missing | Data exposure |
| Batching abuse | Many operations in one request | Rate limit bypass |
| Injection via variables | Unvalidated variable input | SQL/NoSQL injection |

## Disable Introspection in Production

```javascript
// SECURE — Disable introspection in production
const { ApolloServer } = require('@apollo/server');

const server = new ApolloServer({
  typeDefs,
  resolvers,
  introspection: process.env.NODE_ENV !== 'production',
});
```

## Query Depth and Complexity Limiting

```javascript
// SECURE — Limit query depth and complexity
const depthLimit = require('graphql-depth-limit');
const { createComplexityLimitRule } = require('graphql-validation-complexity');

const server = new ApolloServer({
  typeDefs,
  resolvers,
  validationRules: [
    depthLimit(5),                    // Max 5 levels of nesting
    createComplexityLimitRule(1000),   // Max complexity score of 1000
  ],
});
```

### Why This Matters

Without depth limiting, an attacker can craft recursive queries that consume exponential server resources by nesting relationships deeply (e.g., `user.friends.friends.friends...`).

## Field-Level Authorization

```javascript
// SECURE — Authorization in resolvers, not just the schema
const resolvers = {
  Query: {
    user: async (_, { id }, context) => {
      if (!context.user) throw new AuthenticationError('Login required');

      const user = await db.getUser(id);

      // Authorization check
      if (user.id !== context.user.id && !context.user.isAdmin) {
        throw new ForbiddenError('Access denied');
      }

      return user;
    },
  },
  User: {
    email: (parent, _, context) => {
      // Field-level auth — only show email to self or admin
      if (parent.id === context.user.id || context.user.isAdmin) {
        return parent.email;
      }
      return null;
    },
  },
};
```

## Rate Limiting for GraphQL

Standard per-endpoint rate limiting does not work well for GraphQL since all queries hit the same endpoint. Rate limit by query complexity instead.

```javascript
// SECURE — Rate limit by query complexity
const costAnalysis = require('graphql-cost-analysis');

const server = new ApolloServer({
  validationRules: [
    costAnalysis({
      maximumCost: 1000,
      defaultCost: 1,
      variables: req.body.variables,
      onComplete: (cost) => {
        rateLimiter.consume(context.user.id, cost);
      },
    }),
  ],
});
```

## Batching Protection

```javascript
// SECURE — Limit the number of operations per request
app.use('/graphql', (req, res, next) => {
  if (Array.isArray(req.body)) {
    if (req.body.length > 5) {
      return res.status(400).json({ error: 'Too many operations in batch' });
    }
  }
  next();
});
```

## How to Test

1. Try introspection query in production — should be disabled
2. Send deeply nested queries — should be rejected
3. Access other users' fields — should be denied
4. Send batch queries with many operations — should be limited
5. Test variable input with injection payloads

## See Also

- hub/api-security.md
- research/owasp/broken-access-control.md
- research/api-security/rate-limiting.md
- research/api-security/input-validation.md
