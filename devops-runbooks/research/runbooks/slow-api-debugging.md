---
title: "Slow API Endpoint Debugging Runbook"
tags: [API, latency, performance, debugging, slow, runbook, P1]
content_type: research
domain: operations
---

# Slow API Endpoint Debugging

## The Question

How do I debug a slow API endpoint in production?

## Symptoms

- p99 latency exceeding SLO threshold
- Users reporting slow page loads or timeouts
- Timeout errors in application logs
- Upstream services timing out on your API

## Severity: P1 (P0 if causing timeouts for most users)

## Step 1: Quantify the problem (3 minutes)

```bash
# Check current latency from outside
curl -o /dev/null -s -w "DNS: %{time_namelookup}s\nConnect: %{time_connect}s\nTTFB: %{time_starttransfer}s\nTotal: %{time_total}s\n" \
  https://api.example.com/slow-endpoint

# Check latency metrics (Datadog/Prometheus)
# Datadog: p50, p95, p99 by endpoint
# Prometheus:
# histogram_quantile(0.99, rate(http_request_duration_seconds_bucket{handler="/api/endpoint"}[5m]))

# Compare to baseline: Is this endpoint normally fast?
# Check your APM tool for historical latency of this endpoint
```

## Step 2: Identify the bottleneck (5 minutes)

```bash
# Check application logs for slow queries
kubectl logs -n production deploy/api-server --since=10m | grep -i "slow\|timeout\|duration"

# Check database query performance
psql -h db.example.com -U admin -c "
SELECT pid, now() - query_start AS duration, query, state
FROM pg_stat_activity
WHERE state = 'active'
AND now() - query_start > interval '1 second'
ORDER BY duration DESC;
"

# Check for lock contention
psql -h db.example.com -U admin -c "
SELECT blocked_locks.pid AS blocked_pid,
  blocking_locks.pid AS blocking_pid,
  blocked_activity.query AS blocked_query,
  blocking_activity.query AS blocking_query
FROM pg_catalog.pg_locks blocked_locks
JOIN pg_catalog.pg_stat_activity blocked_activity ON blocked_activity.pid = blocked_locks.pid
JOIN pg_catalog.pg_locks blocking_locks ON blocking_locks.locktype = blocked_locks.locktype
JOIN pg_catalog.pg_stat_activity blocking_activity ON blocking_activity.pid = blocking_locks.pid
WHERE NOT blocked_locks.granted;
"

# Check if it is a specific downstream service
kubectl exec -n production deploy/api-server -- curl -o /dev/null -s -w "%{time_total}\n" http://downstream-service:8080/health

# Check network latency between services
kubectl exec -n production deploy/api-server -- ping -c 3 downstream-service
```

## Step 3: Common causes and fixes

### Cause: Slow database query

```bash
# Find the slow query
psql -h db.example.com -U admin -c "
SELECT query, calls, mean_exec_time, total_exec_time
FROM pg_stat_statements
ORDER BY mean_exec_time DESC
LIMIT 10;
"

# Check if an index is missing
psql -h db.example.com -U admin -c "EXPLAIN ANALYZE SELECT * FROM orders WHERE user_id = 12345;"
# Look for "Seq Scan" on large tables — means missing index

# Quick fix: Add an index
psql -h db.example.com -U admin -c "CREATE INDEX CONCURRENTLY idx_orders_user_id ON orders(user_id);"
```

### Cause: Connection pool exhaustion

```bash
# Check active connections
psql -h db.example.com -U admin -c "
SELECT state, count(*) FROM pg_stat_activity GROUP BY state;
"

# Kill idle connections
psql -h db.example.com -U admin -c "
SELECT pg_terminate_backend(pid) FROM pg_stat_activity
WHERE state = 'idle' AND query_start < NOW() - INTERVAL '5 minutes';
"
```

### Cause: Downstream service slow

```bash
# Identify which downstream is slow
kubectl exec -n production deploy/api-server -- \
  curl -o /dev/null -s -w "Service X: %{time_total}s\n" http://service-x:8080/health

# Check downstream service health
kubectl top pods -n production -l app=service-x
kubectl logs -n production deploy/service-x --since=5m | tail -20
```

### Cause: Resource contention (CPU/memory)

```bash
# Check pod resource usage
kubectl top pods -n production -l app=api-server

# Check if CPU throttling is occurring
kubectl exec -n production deploy/api-server -- cat /sys/fs/cgroup/cpu/cpu.stat
# Look for "nr_throttled" — high number means CPU limits are too low
```

## Step 4: Verify improvement

```bash
# Re-run the latency test
curl -o /dev/null -s -w "TTFB: %{time_starttransfer}s Total: %{time_total}s\n" \
  https://api.example.com/slow-endpoint

# Check error rates are dropping
# Monitor your APM dashboard for the affected endpoint
```

## Escalation

If latency does not improve after 30 minutes:
1. Escalate to backend engineering team
2. Consider rate-limiting the slow endpoint to protect other endpoints
3. Enable circuit breaker if the slow endpoint is cascading failures

## Related

- `research/runbooks/database-outage.md` — If the database is the bottleneck
- `research/runbooks/high-cpu-memory-disk.md` — Resource issues
- `research/monitoring/slo-sli-guide.md` — Latency SLO definitions
- `entities/datadog.md` — APM for tracing slow requests
