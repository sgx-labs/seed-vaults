---
title: "Database Scaling"
tags: [database, scaling, connection-pooling, read-replicas, sharding, pgbouncer, supabase, neon]
content_type: research
domain: typescript-fullstack
---

# Database Scaling

## The Core Idea

Most Next.js apps hit database scaling issues in this order: connection exhaustion (serverless), slow queries (missing indexes), read bottleneck (single database), and finally data volume (sharding). Solve them in that order.

## Connection Pooling — The First Wall

Serverless functions create a new database connection per invocation. 100 concurrent requests = 100 connections. PostgreSQL defaults to 100 max connections.

```
                 Without Pooling               With Pooling
Serverless Fn 1 ──> DB Connection 1    Fn 1 ──┐
Serverless Fn 2 ──> DB Connection 2    Fn 2 ──┤──> Pooler ──> 5 DB connections
Serverless Fn 3 ──> DB Connection 3    Fn 3 ──┤
...                                    Fn N ──┘
Serverless Fn N ──> DB Connection N
```

### Supabase Pooler (Supavisor)

```
# Direct connection (for migrations)
DATABASE_URL="postgresql://user:pass@db.xxx.supabase.co:5432/postgres"

# Pooled connection (for app queries)
DATABASE_URL="postgresql://user:pass@xxx.pooler.supabase.com:6543/postgres?pgbouncer=true"
```

### Neon Serverless Driver

```typescript
import { neon } from "@neondatabase/serverless";
import { drizzle } from "drizzle-orm/neon-http";

// HTTP-based — no persistent connection needed
const sql = neon(process.env.DATABASE_URL!);
const db = drizzle(sql);
```

### PgBouncer (Self-Hosted)

```ini
# pgbouncer.ini
[databases]
mydb = host=localhost port=5432 dbname=mydb

[pgbouncer]
listen_port = 6543
pool_mode = transaction    # Best for serverless
max_client_conn = 1000
default_pool_size = 20
```

## Query Optimization — The Second Wall

### Add Indexes

```sql
-- Find slow queries (PostgreSQL)
SELECT query, calls, mean_exec_time, total_exec_time
FROM pg_stat_statements
ORDER BY mean_exec_time DESC
LIMIT 20;

-- Common indexes for a typical app
CREATE INDEX idx_posts_author ON posts (author_id);
CREATE INDEX idx_posts_published_created ON posts (published, created_at DESC)
  WHERE published = true;  -- Partial index
CREATE INDEX idx_users_email ON users (email);
```

### Use `select` Instead of `include`

```typescript
// Bad — fetches entire user + all posts + all comments
const user = await db.user.findUnique({
  where: { id },
  include: { posts: { include: { comments: true } } },
});

// Good — fetches only what the UI needs
const user = await db.user.findUnique({
  where: { id },
  select: {
    id: true,
    name: true,
    posts: {
      select: { id: true, title: true },
      where: { published: true },
      take: 10,
    },
  },
});
```

### Avoid N+1 Queries

```typescript
// N+1 — one query per post to get author
const posts = await db.post.findMany();
for (const post of posts) {
  post.author = await db.user.findUnique({ where: { id: post.authorId } }); // N queries
}

// Fixed — single query with relation
const posts = await db.post.findMany({
  include: { author: { select: { name: true } } }, // 1 query with JOIN
});
```

## Read Replicas — The Third Wall

When reads overwhelm a single database, add read replicas.

```typescript
// Prisma with read replicas
import { PrismaClient } from "@prisma/client";
import { readReplicas } from "@prisma/extension-read-replicas";

const db = new PrismaClient().$extends(
  readReplicas({
    url: process.env.DATABASE_REPLICA_URL!,
  })
);

// Reads automatically go to replica
const users = await db.user.findMany(); // -> replica

// Writes go to primary
await db.user.create({ data: { ... } }); // -> primary

// Force read from primary (after write, for consistency)
const user = await db.$primary().user.findUnique({ where: { id } });
```

## Caching Layer

Before adding read replicas, consider caching.

```typescript
import { Redis } from "@upstash/redis";

const redis = Redis.fromEnv();

async function getUser(id: string) {
  // Check cache
  const cached = await redis.get<User>(`user:${id}`);
  if (cached) return cached;

  // Fetch from DB
  const user = await db.user.findUnique({ where: { id } });
  if (user) {
    await redis.set(`user:${id}`, user, { ex: 300 }); // Cache for 5 min
  }
  return user;
}

// Invalidate on update
async function updateUser(id: string, data: UpdateUserInput) {
  const user = await db.user.update({ where: { id }, data });
  await redis.del(`user:${id}`);
  return user;
}
```

## Scaling Stages

| Stage | Symptom | Solution |
|-------|---------|----------|
| 1 | "too many connections" errors | Connection pooler |
| 2 | Slow queries (> 100ms) | Indexes, query optimization |
| 3 | Read latency under load | Redis cache + read replicas |
| 4 | Write bottleneck | Vertical scaling (bigger instance) |
| 5 | Data too large for one server | Sharding (rare for most apps) |

## See Also

- `hub/database-patterns.md` — Prisma/Drizzle patterns
- `research/prisma-vs-drizzle.md` — Edge runtime considerations
- `hub/performance-patterns.md` — Application-level caching
