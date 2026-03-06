---
title: "Database Migrations Without Downtime"
tags: [guide, database, migrations, zero-downtime, expand-contract, prisma, postgresql]
content_type: guide
domain: typescript-fullstack
---

# Database Migrations Without Downtime

## The Expand/Contract Pattern

Every safe migration follows two phases:
1. **Expand** — add new things (columns, tables, indexes). Code works with old AND new schema.
2. **Contract** — remove old things. Code only uses new schema.

Never do both in one deployment.

## Adding a Required Column

**Wrong** (causes downtime):
```sql
ALTER TABLE users ADD COLUMN display_name TEXT NOT NULL;
-- Fails: existing rows have no value
```

**Right** (zero downtime):

```
Step 1: Add as optional
  ALTER TABLE users ADD COLUMN display_name TEXT;

Step 2: Deploy code that writes to display_name on create/update

Step 3: Backfill existing rows
  UPDATE users SET display_name = name WHERE display_name IS NULL;

Step 4: Make NOT NULL
  ALTER TABLE users ALTER COLUMN display_name SET NOT NULL;
```

```typescript
// Prisma migration sequence:
// Migration 1: Add optional column
// prisma/migrations/001_add_display_name/migration.sql
ALTER TABLE "users" ADD COLUMN "display_name" TEXT;

// Migration 2: After backfill, make required
// prisma/migrations/002_require_display_name/migration.sql
UPDATE "users" SET "display_name" = "name" WHERE "display_name" IS NULL;
ALTER TABLE "users" ALTER COLUMN "display_name" SET NOT NULL;
```

## Renaming a Column

**Wrong**: `ALTER TABLE users RENAME COLUMN name TO display_name;`

**Right**:
```
Step 1: Add new column
  ALTER TABLE users ADD COLUMN display_name TEXT;

Step 2: Deploy code that reads from both, writes to both
  SELECT COALESCE(display_name, name) AS display_name FROM users;

Step 3: Backfill
  UPDATE users SET display_name = name WHERE display_name IS NULL;

Step 4: Deploy code that only uses display_name

Step 5: Drop old column
  ALTER TABLE users DROP COLUMN name;
```

## Adding an Index

```sql
-- Wrong: locks the table during creation
CREATE INDEX idx_posts_author ON posts (author_id);

-- Right: doesn't lock the table
CREATE INDEX CONCURRENTLY idx_posts_author ON posts (author_id);
```

**Prisma note**: Prisma doesn't support `CONCURRENTLY` in generated migrations. Add it manually:

```sql
-- prisma/migrations/003_add_index/migration.sql
-- CreateIndex
CREATE INDEX CONCURRENTLY "idx_posts_author" ON "posts"("author_id");
```

## Dropping a Column

```
Step 1: Stop reading from the column in code
Step 2: Deploy
Step 3: Stop writing to the column in code
Step 4: Deploy
Step 5: Drop the column
  ALTER TABLE users DROP COLUMN old_column;
```

## Changing a Column Type

```
Step 1: Add new column with new type
Step 2: Deploy code that writes to both
Step 3: Backfill + convert data
Step 4: Deploy code that reads from new column
Step 5: Drop old column
```

## Prisma Migration Checklist

- [ ] Run `prisma migrate dev` locally first
- [ ] Review the generated SQL (`prisma/migrations/*/migration.sql`)
- [ ] Check for `DROP`, `ALTER ... NOT NULL`, `RENAME` — they need the expand/contract pattern
- [ ] Add `CONCURRENTLY` to index creation manually
- [ ] Test migration on a staging database with production-like data
- [ ] Have a rollback plan (reverse migration SQL)

## See Also

- `hub/database-patterns.md` — Database patterns
- `entities/prisma-schema-reference.md` — Prisma schema reference
- `research/database-scaling.md` — Scaling considerations
