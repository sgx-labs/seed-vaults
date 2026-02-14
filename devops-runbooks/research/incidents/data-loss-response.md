---
title: "Data Loss and Corruption Response Runbook"
tags: [data-loss, corruption, backup, restore, disaster-recovery, runbook, P0]
content_type: research
domain: operations
---

# Data Loss and Corruption Response

## The Question

How do I handle data loss or corruption?

## Severity: P0

Data loss is always P0 until scope is understood. Every minute counts — the longer you wait, the more data diverges from the last good state.

## Step 1: Stop the bleeding (2 minutes)

**CRITICAL: Prevent further writes to the affected data store.**

```bash
# PostgreSQL: Set database to read-only
psql -h db.example.com -U admin -c "ALTER DATABASE production SET default_transaction_read_only = on;"

# MySQL: Set read-only mode
mysql -h db.example.com -u admin -p -e "SET GLOBAL read_only = ON;"

# Application level: Enable maintenance mode or circuit breaker
kubectl set env deployment/api-server -n production MAINTENANCE_MODE=true
kubectl rollout restart deployment/api-server -n production
```

## Step 2: Assess scope (10 minutes)

```bash
# PostgreSQL: Check table sizes and row counts
psql -h db.example.com -U admin -c "
SELECT schemaname, tablename, n_live_tup
FROM pg_stat_user_tables
ORDER BY n_live_tup DESC;
"

# Check for recent destructive operations in logs
grep -i "delete\|drop\|truncate\|update" /var/log/postgresql/postgresql.log | tail -50

# Check replication lag (if using replicas)
psql -h db.example.com -U admin -c "SELECT * FROM pg_stat_replication;"

# Check most recent backup
aws s3 ls s3://your-backup-bucket/db-backups/ --recursive | sort | tail -5
```

**Key questions to answer:**
- What data is affected (which tables, which records)?
- When did the corruption/loss start?
- Is the replica also affected?
- What is the most recent clean backup?

## Step 3: Choose recovery strategy

### Option A: Recover from replica (fastest, if replica is clean)

```bash
# Verify replica is not corrupted
psql -h db-replica.example.com -U admin -c "
SELECT count(*) FROM critical_table;
"

# Promote replica to primary
# AWS RDS:
aws rds promote-read-replica --db-instance-identifier mydb-replica

# Update application connection strings
kubectl set env deployment/api-server -n production \
  DATABASE_URL="postgresql://user:pass@db-replica.example.com:5432/production"
kubectl rollout restart deployment/api-server -n production
```

### Option B: Point-in-time recovery (precise, takes longer)

```bash
# AWS RDS: Restore to a specific point in time
aws rds restore-db-instance-to-point-in-time \
  --source-db-instance-identifier mydb \
  --target-db-instance-identifier mydb-pitr \
  --restore-time "2026-02-14T02:30:00Z"

# Wait for instance to become available
aws rds wait db-instance-available --db-instance-identifier mydb-pitr

# Verify data integrity on restored instance
psql -h mydb-pitr.example.com -U admin -c "SELECT count(*) FROM critical_table;"
```

### Option C: Restore from backup (slowest, most complete)

```bash
# Download latest backup
aws s3 cp s3://your-backup-bucket/db-backups/latest.sql.gz /tmp/

# Restore to a fresh database
gunzip /tmp/latest.sql.gz
psql -h db-restore.example.com -U admin -f /tmp/latest.sql

# Verify restore
psql -h db-restore.example.com -U admin -c "
SELECT tablename, n_live_tup FROM pg_stat_user_tables ORDER BY tablename;
"
```

## Step 4: Validate recovered data

```bash
# Compare row counts between original and restored
psql -h db.example.com -U admin -c "SELECT count(*) FROM users;"
psql -h db-restore.example.com -U admin -c "SELECT count(*) FROM users;"

# Check data integrity
psql -h db-restore.example.com -U admin -c "
SELECT COUNT(*) FROM orders WHERE total < 0;  -- sanity check
SELECT MAX(created_at) FROM orders;  -- check latest record
"
```

## Step 5: Swap to recovered database

```bash
# Update DNS or connection string to point to restored database
kubectl set env deployment/api-server -n production \
  DATABASE_URL="postgresql://user:pass@db-restore.example.com:5432/production"

# Remove read-only mode
kubectl set env deployment/api-server -n production MAINTENANCE_MODE=false
kubectl rollout restart deployment/api-server -n production

# Verify application is working
curl -s https://your-service.example.com/health
```

## Post-Recovery

1. Document the gap between backup and loss (this is lost data)
2. Notify affected users if required
3. Review backup frequency — is it sufficient?
4. Test backup restoration process (schedule quarterly drills)
5. Conduct post-mortem within 48 hours

## Related

- `research/runbooks/database-outage.md` — Database not responding
- `research/infrastructure/controlled-failover.md` — Database failover
- `research/incidents/incident-classification.md` — Severity classification
