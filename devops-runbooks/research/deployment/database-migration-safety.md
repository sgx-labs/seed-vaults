---
title: "Database Migration Safety Guide"
tags: [database, migration, schema, deployment, safety, runbook, SQL, locking, rollback]
content_type: research
domain: operations
---

# Database Migration Safety Guide

## The Question

How do I run database migrations safely in production?

## Rule: Every migration must be reversible or forward-compatible

If a migration cannot be undone, you need a rollback plan BEFORE running it.

## Pre-Migration Checklist

- [ ] Migration tested in staging with production-sized data
- [ ] Rollback migration written and tested
- [ ] Estimated lock duration calculated for large tables
- [ ] On-call engineer notified
- [ ] Maintenance window scheduled (if locking migration)
- [ ] Backup taken immediately before migration

## Safe Migration Patterns

### Adding a column (safe)

```sql
-- Safe: Adds column with default value. No lock on reads in PostgreSQL 11+.
ALTER TABLE orders ADD COLUMN status VARCHAR(20) DEFAULT 'pending';
```

### Adding an index (use CONCURRENTLY)

```sql
-- UNSAFE: Locks the table for the entire build time
CREATE INDEX idx_orders_user ON orders(user_id);

-- SAFE: Builds index without blocking writes
CREATE INDEX CONCURRENTLY idx_orders_user ON orders(user_id);

-- Note: CONCURRENTLY cannot be used inside a transaction
```

### Renaming a column (multi-step)

```sql
-- Step 1: Add new column
ALTER TABLE orders ADD COLUMN total_amount NUMERIC(10,2);

-- Step 2: Backfill data (in batches)
UPDATE orders SET total_amount = amount WHERE total_amount IS NULL LIMIT 10000;
-- Repeat until all rows updated

-- Step 3: Deploy app code that reads/writes BOTH columns
-- Step 4: Verify all reads come from new column
-- Step 5: Drop old column
ALTER TABLE orders DROP COLUMN amount;
```

### Dropping a column (multi-step)

```sql
-- Step 1: Stop writing to the column in application code
-- Step 2: Deploy and verify no code references the column
-- Step 3: Drop the column
ALTER TABLE orders DROP COLUMN legacy_field;

-- NEVER drop a column that application code still references
```

## Running Migrations

```bash
# Step 1: Take a backup
pg_dump -h db.example.com -U admin -Fc production > /tmp/pre-migration-backup.dump

# Step 2: Check table size and estimate lock time
psql -h db.example.com -U admin -c "
SELECT pg_size_pretty(pg_total_relation_size('orders')) as table_size,
       n_live_tup as row_count
FROM pg_stat_user_tables WHERE relname = 'orders';
"

# Step 3: Run migration
# Use your migration tool (Rails, Django, Flyway, etc.)
# Or run SQL directly:
psql -h db.example.com -U admin -f migration_001.sql

# Step 4: Verify migration succeeded
psql -h db.example.com -U admin -c "\d orders"  -- Check schema
psql -h db.example.com -U admin -c "SELECT count(*) FROM orders;"  -- Check data

# Step 5: Monitor application for errors
kubectl logs -n production deploy/api-server --since=5m | grep -i "error\|column\|migration"
```

## Dangerous Operations (Require Maintenance Window)

| Operation | Risk | Mitigation |
|-----------|------|------------|
| `ALTER TABLE ... ADD COLUMN ... NOT NULL` (no default) | Table lock in older PG | Add with default, then add NOT NULL |
| `CREATE INDEX` (without CONCURRENTLY) | Table lock | Always use CONCURRENTLY |
| `ALTER TABLE ... ALTER COLUMN TYPE` | Full table rewrite | Add new column, backfill, swap |
| `DROP TABLE` or `TRUNCATE` | Data loss | Rename to `_deprecated_` first, drop after 30 days |

## Rollback

```bash
# If migration broke something:

# Option A: Run rollback migration
psql -h db.example.com -U admin -f migration_001_rollback.sql

# Option B: Restore from backup (last resort)
pg_restore -h db.example.com -U admin -d production /tmp/pre-migration-backup.dump
```

## Related

- `research/deployment/rollback-procedures.md` — Rollback strategies
- `research/runbooks/database-outage.md` — Database recovery
- `research/incidents/data-loss-response.md` — Data loss recovery
- `hub/deployment.md` — Deployment overview
