---
title: "Disaster Recovery Testing Guide"
tags: [disaster-recovery, DR, testing, drill, chaos-engineering, resilience]
content_type: research
domain: operations
---

# Disaster Recovery Testing

## The Question

How do I test disaster recovery so I know it works before I need it?

## Why Test

A backup you have never restored is not a backup. A failover you have never tested is not a failover. The time to discover your DR does not work is NOT during an actual disaster.

## Quarterly DR Test Schedule

| Quarter | Test | Duration | Scope |
|---------|------|----------|-------|
| Q1 | Database backup restore | 2 hours | Restore latest backup to test environment |
| Q2 | Region failover | 4 hours | Full failover to secondary region |
| Q3 | Service failure simulation | 2 hours | Kill a critical service, verify graceful degradation |
| Q4 | Full disaster simulation | 4 hours | Simulate complete primary failure |

## Test 1: Database Backup Restore

**Goal:** Verify backups are valid and can be restored within RTO.

```bash
# Step 1: Identify latest backup
aws rds describe-db-snapshots --db-instance-identifier mydb \
  --query 'DBSnapshots | sort_by(@, &SnapshotCreateTime) | [-1].[DBSnapshotIdentifier,SnapshotCreateTime]'

# Step 2: Restore to a test instance
aws rds restore-db-instance-from-db-snapshot \
  --db-instance-identifier mydb-dr-test \
  --db-snapshot-identifier LATEST_SNAPSHOT_ID \
  --db-instance-class db.t3.medium

# Step 3: Wait for restore to complete
aws rds wait db-instance-available --db-instance-identifier mydb-dr-test

# Step 4: Verify data integrity
psql -h mydb-dr-test.example.com -U admin -c "
SELECT 'users' as tbl, count(*) FROM users
UNION ALL
SELECT 'orders', count(*) FROM orders
UNION ALL
SELECT 'payments', count(*) FROM payments;
"

# Step 5: Check latest data timestamp
psql -h mydb-dr-test.example.com -U admin -c "
SELECT MAX(created_at) as latest_record FROM orders;
"

# Step 6: Clean up test instance
aws rds delete-db-instance --db-instance-identifier mydb-dr-test \
  --skip-final-snapshot
```

**Record:** Restore time, data freshness (gap from backup to latest record), any issues.

## Test 2: Service Failure Simulation

**Goal:** Verify the system degrades gracefully when a service dies.

```bash
# Step 1: Pick a non-database service (payment gateway, search, cache)
# Step 2: Kill it in a test environment
kubectl scale deployment/search-service -n staging --replicas=0

# Step 3: Verify graceful degradation
curl -s https://staging.example.com/health
# Should show degraded, not down

curl -s https://staging.example.com/search?q=test
# Should return error message, not 500

# Step 4: Restore the service
kubectl scale deployment/search-service -n staging --replicas=3

# Step 5: Verify full recovery
kubectl rollout status deployment/search-service -n staging
curl -s https://staging.example.com/search?q=test
```

## Test 3: Failover Test

See `research/infrastructure/controlled-failover.md` for the full procedure. Run it against staging first, then production (during a maintenance window).

## Game Day Checklist

A "game day" is a planned disaster simulation with the whole team.

```
Before:
- [ ] Schedule during business hours (not Friday)
- [ ] Notify all stakeholders
- [ ] Prepare rollback plan if the test goes wrong
- [ ] Assign an observer to document everything
- [ ] Put status page in maintenance mode (if testing prod)

During:
- [ ] Start the simulation
- [ ] Time how long detection takes
- [ ] Time how long recovery takes
- [ ] Document what breaks and what works
- [ ] Verify all runbooks are accurate

After:
- [ ] Record MTTD and MTTR for the simulation
- [ ] Document gaps in runbooks or tooling
- [ ] Create action items for improvements
- [ ] Share results with the team
- [ ] Update runbooks with any corrections
```

## What To Measure

| Metric | Target | Actual |
|--------|--------|--------|
| Time to detect simulated failure | <5 min | ___ |
| Time to recover (MTTR) | <30 min | ___ |
| Backup restore time (RTO) | <1 hour | ___ |
| Data loss window (RPO) | <1 hour | ___ |
| Runbook accuracy | 100% steps work | ___% |

## Related

- `research/infrastructure/controlled-failover.md` — Failover procedure
- `research/incidents/data-loss-response.md` — Backup restore process
- `research/postmortems/blameless-postmortem.md` — Document DR test results
- `hub/infrastructure.md` — Infrastructure overview
