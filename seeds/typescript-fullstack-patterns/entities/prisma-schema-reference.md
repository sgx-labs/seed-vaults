---
title: "Prisma Schema Quick Reference"
tags: [prisma, schema, reference, entity, relations, types, attributes]
content_type: entity
domain: typescript-fullstack
---

# Prisma Schema Quick Reference

## Field Types

| Prisma Type | PostgreSQL | TypeScript |
|-------------|-----------|------------|
| `String` | `text` / `varchar` | `string` |
| `Int` | `integer` | `number` |
| `BigInt` | `bigint` | `bigint` |
| `Float` | `double precision` | `number` |
| `Decimal` | `decimal` | `Decimal` |
| `Boolean` | `boolean` | `boolean` |
| `DateTime` | `timestamp` | `Date` |
| `Json` | `jsonb` | `JsonValue` |
| `Bytes` | `bytea` | `Buffer` |

## Attributes

```prisma
model User {
  id        String   @id @default(cuid())    // Primary key + auto-generate
  email     String   @unique                  // Unique constraint
  name      String   @db.VarChar(100)         // Database-specific type
  bio       String?                            // Optional (nullable)
  role      Role     @default(USER)           // Default value
  createdAt DateTime @default(now())          // Auto timestamp
  updatedAt DateTime @updatedAt              // Auto-update timestamp

  @@index([email])                            // Single-column index
  @@index([name, createdAt])                  // Composite index
  @@unique([email, role])                     // Unique constraint on multiple columns
  @@map("users")                              // Custom table name
}
```

## ID Strategies

```prisma
id String @id @default(cuid())     // CUID — collision-resistant, sortable
id String @id @default(uuid())     // UUID v4 — standard, not sortable
id Int    @id @default(autoincrement()) // Auto-increment integer
id String @id @default(nanoid())   // NanoID — short, URL-friendly (with extension)
```

## Relations

```prisma
// One-to-many
model User {
  id    String @id @default(cuid())
  posts Post[]
}

model Post {
  id       String @id @default(cuid())
  author   User   @relation(fields: [authorId], references: [id])
  authorId String
}

// Many-to-many (implicit)
model Post {
  id   String @id @default(cuid())
  tags Tag[]
}

model Tag {
  id    String @id @default(cuid())
  posts Post[]
}

// Many-to-many (explicit — for extra fields)
model PostTag {
  post      Post     @relation(fields: [postId], references: [id])
  postId    String
  tag       Tag      @relation(fields: [tagId], references: [id])
  tagId     String
  assignedAt DateTime @default(now())

  @@id([postId, tagId])
}

// Self-relation (e.g., followers)
model User {
  id        String @id @default(cuid())
  followers User[] @relation("UserFollows")
  following User[] @relation("UserFollows")
}
```

## Enums

```prisma
enum Role {
  USER
  EDITOR
  ADMIN
}

enum OrderStatus {
  PENDING
  PROCESSING
  SHIPPED
  DELIVERED
  CANCELLED
}
```

## Cascade Deletes

```prisma
model Post {
  author   User @relation(fields: [authorId], references: [id], onDelete: Cascade)
  authorId String
}
// onDelete: Cascade | SetNull | Restrict | NoAction | SetDefault
```

## Common Commands

```bash
npx prisma generate        # Generate client from schema
npx prisma migrate dev     # Create and apply migration
npx prisma db push         # Push schema changes (no migration)
npx prisma studio          # Open database browser
npx prisma db seed         # Run seed script
npx prisma format          # Format schema file
```

## See Also

- `hub/database-patterns.md` — Production database patterns
- `research/prisma-vs-drizzle.md` — Comparison with Drizzle
