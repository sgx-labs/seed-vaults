---
title: "Database Outage Runbook"
tags: [database, outage, postgresql, mysql, recovery, runbook, P0]
content_type: research
domain: operations
---

# Database Outage Runbook

## The Question

What do I do when the database is not responding?

## Symptoms

- Application returning 500 errors with "connection refused" or "connection timed out"
- Connection pool exhausted warnings in application logs
- Replication lag increasing or replicas disconnected
- Database process not running or unresponsive

## Severity: P0

Database outage affects all users. Classify as P0 immediately.

## Step 1: Verify the outage (2 minutes)

```bash
# Test connectivity
pg_isready -h db.example.com -p 5432
# Expected: "accepting connections" or "no response"

# MySQL equivalent
mysqladmin ping -h db.example.com -u monitor

# Check if you can connect
psql -h db.example.com -U monitor -c "SELECT 1;" -t

# Check from application pod (if K8s)
kubectl exec -n production deploy/api-server -- \
  pg_isready -h db-service -p 5432
```

## Step 2: Diagnose the cause (5 minutes)

```bash
# Check if the database process is running
# On the DB server:
systemctl status postgresql
ps aux | grep postgres

# Check disk space (full disk = database crash)
df -h /var/lib/postgresql/

# Check memory (OOM killer may have killed the process)
dmesg | grep -i "oom\|killed" | tail -10

# Check PostgreSQL logs
tail -100 /var/log/postgresql/postgresql-*.log

# Check connection count (connection pool exhaustion)
psql -h db.example.com -U admin -c "SELECT count(*) FROM pg_stat_activity;"
psql -h db.example.com -U admin -c "SHOW max_connections;"

# Check for long-running queries (potential lock)
psql -h db.example.com -U admin -c "
SELECT pid, now() - pg_stat_activity.query_start AS duration, query, state
FROM pg_stat_activity
WHERE state != 'idle'
ORDER BY duration DESC
LIMIT 10;
"

# AWS RDS: Check status
aws rds describe-db-instances --db-instance-identifier mydb \
  --query 'DBInstances[0].[DBInstanceStatus,AvailabilityZone]' --output text

# AWS RDS: Check recent events
aws rds describe-events --source-identifier mydb \
  --source-type db-instance --duration 60
```

## Step 3: Apply fix based on cause

### Cause: Database process crashed

```bash
# Restart PostgreSQL
sudo systemctl restart postgresql

# Verify it started
pg_isready -h localhost -p 5432

# Check logs for crash reason
tail -50 /var/log/postgresql/postgresql-*.log
```

### Cause: Disk full

```bash
# Find large files
du -sh /var/lib/postgresql/*/main/* | sort -rh | head -10

# Clear WAL files if safe (DANGEROUS — only if you have backups)
# pg_archivecleanup /var/lib/postgresql/14/main/pg_wal OLDEST_NEEDED_WAL

# Clear old temp files
find /tmp -name "pgsql_tmp*" -mtime +1 -delete

# Expand disk (AWS EBS)
aws ec2 modify-volume --volume-id vol-12345678 --size 200
# Then resize filesystem
sudo resize2fs /dev/xvdf
```

### Cause: Connection pool exhaustion

```bash
# Kill idle connections
psql -h db.example.com -U admin -c "
SELECT pg_terminate_backend(pid)
FROM pg_stat_activity
WHERE state = 'idle'
AND query_start < NOW() - INTERVAL '10 minutes';
"

# Kill long-running queries
psql -h db.example.com -U admin -c "
SELECT pg_terminate_backend(pid)
FROM pg_stat_activity
WHERE state = 'active'
AND query_start < NOW() - INTERVAL '30 minutes';
"
```

### Cause: Replication lag (read replicas stale)

```bash
# Check replication status
psql -h db.example.com -U admin -c "SELECT * FROM pg_stat_replication;"

# Check replica lag in bytes
psql -h db.example.com -U admin -c "
SELECT client_addr,
  pg_wal_lsn_diff(pg_current_wal_lsn(), replay_lsn) AS lag_bytes
FROM pg_stat_replication;
"

# AWS RDS: Check replica lag
aws cloudwatch get-metric-statistics \
  --namespace AWS/RDS --metric-name ReplicaLag \
  --dimensions Name=DBInstanceIdentifier,Value=mydb-replica \
  --start-time $(date -u -d '1 hour ago' +%Y-%m-%dT%H:%M:%S) \
  --end-time $(date -u +%Y-%m-%dT%H:%M:%S) \
  --period 300 --statistics Average
```

## Step 4: Verify recovery

```bash
# Test application connectivity
curl -s https://your-service.example.com/health

# Check error rates are returning to normal
# (Check monitoring dashboard)

# Verify active connections are healthy
psql -h db.example.com -U admin -c "
SELECT state, count(*) FROM pg_stat_activity GROUP BY state;
"
```

## Escalation

If the database will not start after 15 minutes:
1. Escalate to database team / DBA
2. Consider failover to replica (`research/infrastructure/controlled-failover.md`)
3. Consider restore from backup (`research/incidents/data-loss-response.md`)

## Related

- `research/incidents/data-loss-response.md` — If data is corrupted
- `research/infrastructure/controlled-failover.md` — Database failover
- `research/runbooks/high-cpu-memory-disk.md` — Resource issues
- `entities/aws.md` — RDS-specific commands
