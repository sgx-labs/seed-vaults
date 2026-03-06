---
title: "Database Patterns with Prisma/Drizzle"
tags: [prisma, drizzle, database, postgresql, migrations, relations, transactions, orm, edge]
content_type: hub
domain: typescript-fullstack
---

# Database Patterns with Prisma/Drizzle

## Overview

Every fullstack TypeScript app needs a database layer. Prisma and Drizzle are the two dominant choices -- both generate TypeScript types, handle migrations, and provide query builders. This note covers production patterns for both, including the edge cases that bite you in production.

## Prisma — The Batteries-Included Choice

### Singleton Pattern (Required)

Next.js hot reloads in development create multiple Prisma clients. The singleton pattern prevents connection exhaustion.

```typescript
// lib/db.ts
import { PrismaClient } from "@prisma/client";

const globalForPrisma = globalThis as unknown as { prisma: PrismaClient };

export const db = globalForPrisma.prisma ?? new PrismaClient({
  log: process.env.NODE_ENV === "development" ? ["query", "warn", "error"] : ["error"],
});

if (process.env.NODE_ENV !== "production") globalForPrisma.prisma = db;
```

### Relations — Select What You Need

```typescript
// Bad — includes everything, sends unnecessary data
const user = await db.user.findUnique({
  where: { id },
  include: { posts: true, comments: true, profile: true },
});

// Good — select only what the UI needs
const user = await db.user.findUnique({
  where: { id },
  select: {
    id: true,
    name: true,
    posts: {
      select: { id: true, title: true, publishedAt: true },
      orderBy: { publishedAt: "desc" },
      take: 10,
    },
  },
});
```

### Transactions — All or Nothing

```typescript
// Interactive transaction — multiple operations, rollback on any failure
const [order, payment] = await db.$transaction(async (tx) => {
  const order = await tx.order.create({
    data: { userId, items: { create: items } },
  });

  const payment = await tx.payment.create({
    data: { orderId: order.id, amount: total, status: "pending" },
  });

  // If this throws, both order and payment are rolled back
  await chargePayment(payment.id);

  return [order, payment];
}, {
  maxWait: 5000,   // Max time to acquire a connection
  timeout: 10000,  // Max transaction duration
});
```

### Soft Deletes

```prisma
model Post {
  id        String    @id @default(cuid())
  title     String
  deletedAt DateTime?

  @@index([deletedAt])
}
```

Use Prisma middleware or extensions to automatically filter soft-deleted records on `findMany`/`findFirst` and convert `delete` to `update`.

## Drizzle — The SQL-Close Choice

### Schema Definition

```typescript
// db/schema.ts
import { pgTable, text, timestamp, integer, boolean } from "drizzle-orm/pg-core";
import { relations } from "drizzle-orm";

export const users = pgTable("users", {
  id: text("id").primaryKey().$defaultFn(() => createId()),
  name: text("name").notNull(),
  email: text("email").notNull().unique(),
  createdAt: timestamp("created_at").defaultNow().notNull(),
});

export const posts = pgTable("posts", {
  id: text("id").primaryKey().$defaultFn(() => createId()),
  title: text("title").notNull(),
  authorId: text("author_id").notNull().references(() => users.id),
  published: boolean("published").default(false).notNull(),
});

export const usersRelations = relations(users, ({ many }) => ({
  posts: many(posts),
}));

export const postsRelations = relations(posts, ({ one }) => ({
  author: one(users, { fields: [posts.authorId], references: [users.id] }),
}));
```

### Queries

```typescript
import { db } from "@/db";
import { users, posts } from "@/db/schema";
import { eq, desc, and, isNull } from "drizzle-orm";

// Type-safe query with relations
const result = await db.query.users.findFirst({
  where: eq(users.id, userId),
  with: {
    posts: {
      where: eq(posts.published, true),
      orderBy: [desc(posts.createdAt)],
      limit: 10,
    },
  },
});

// Raw SQL escape hatch (still type-safe)
const stats = await db.execute(
  sql`SELECT COUNT(*) as count FROM ${posts} WHERE ${posts.authorId} = ${userId}`
);
```

## Migrations Without Downtime

1. **Never rename columns in one step** — add new column, backfill, update app, drop old column
2. **Never drop columns directly** — stop reading first, deploy, then drop
3. **Add indexes concurrently** in PostgreSQL: `CREATE INDEX CONCURRENTLY`
4. **Use expand/contract pattern**: expand schema (additive), deploy code, contract schema (remove old)

See `guides/database-migrations-without-downtime.md` for detailed examples.

## Connection Pooling

**Footgun**: Serverless environments (Vercel, AWS Lambda) can exhaust database connections. Each invocation may create a new connection.

```typescript
// Use a connection pooler (PgBouncer, Supabase Pooler, Neon Pooler)
// .env
DATABASE_URL="postgresql://user:pass@pooler.supabase.com:6543/db?pgbouncer=true"

// For Prisma on serverless
DIRECT_URL="postgresql://user:pass@db.supabase.com:5432/db" // For migrations
DATABASE_URL="postgresql://user:pass@pooler.supabase.com:6543/db" // For queries
```

## Prisma vs Drizzle Decision

| Factor | Prisma | Drizzle |
|--------|--------|---------|
| Learning curve | Lower — schema is intuitive | Higher — closer to SQL |
| Edge runtime | Needs Accelerate/Data Proxy | Native support |
| Type safety | Generated from schema | Schema IS TypeScript |
| Raw SQL | `$queryRaw` | First-class `sql` template |
| Migration DX | Excellent (prisma migrate) | Good (drizzle-kit) |
| Bundle size | Larger (engine binary) | Smaller |
| Ecosystem | Larger, more mature | Growing fast |

## See Also

- `research/prisma-vs-drizzle.md` — Deep comparison
- `research/database-scaling.md` — Connection pooling, read replicas, sharding
- `entities/prisma-schema-reference.md` — Prisma schema quick reference
- `guides/database-migrations-without-downtime.md` — Safe migration guide
- `decisions/database-choice-template.md` — Decision framework
