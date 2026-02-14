---
title: "Cache Failure Runbook (Redis/Memcached)"
tags: [cache, redis, memcached, failure, thundering-herd, runbook, P1]
content_type: research
domain: operations
---

# Cache Failure Runbook

## The Question

What do I do when the cache layer (Redis/Memcached) fails?

## Symptoms

- Application latency spiked (database now handling all reads)
- Redis/Memcached not responding to connections
- Cache hit rate dropped to 0%
- Database CPU/connections spiked (thundering herd)
- Application logs: "connection refused" to cache host

## Severity: P1 (P0 if database cannot handle the uncached load)

## Step 1: Check cache status (2 minutes)

```bash
# Redis: Check connectivity
redis-cli -h cache.example.com ping
# Expected: PONG

# Redis: Check key stats
redis-cli -h cache.example.com info stats | grep -E "keyspace_hits|keyspace_misses|connected_clients|used_memory_human"

# Redis: Check memory usage
redis-cli -h cache.example.com info memory | grep "used_memory_human\|maxmemory_human\|mem_fragmentation_ratio"

# Memcached: Check connectivity
echo "stats" | nc cache.example.com 11211 | head -20

# AWS ElastiCache: Check status
aws elasticache describe-cache-clusters --cache-cluster-id my-cache \
  --query 'CacheClusters[0].[CacheClusterStatus,NumCacheNodes]'
```

## Step 2: Diagnose the cause

### Redis not responding

```bash
# Check if Redis process is running
redis-cli -h cache.example.com info server | grep "redis_version\|uptime_in_seconds"

# Check for max memory reached
redis-cli -h cache.example.com info memory
# If used_memory >= maxmemory, eviction policy is critical

# Check for slow operations
redis-cli -h cache.example.com slowlog get 10

# Check connected clients (too many?)
redis-cli -h cache.example.com info clients | grep "connected_clients\|blocked_clients"
redis-cli -h cache.example.com client list | wc -l
```

### Cache hit rate dropped

```bash
# Calculate hit rate
redis-cli -h cache.example.com info stats | grep "keyspace"
# hit_rate = keyspace_hits / (keyspace_hits + keyspace_misses)

# Check if keys were flushed
redis-cli -h cache.example.com dbsize
# If 0 or very low: someone flushed the cache

# Check expiration
redis-cli -h cache.example.com info keyspace
# Look at "expires" count — mass expiration can cause stampede
```

## Step 3: Fix

### Cache is down — restart it

```bash
# Redis restart
sudo systemctl restart redis

# Verify it came back
redis-cli -h cache.example.com ping

# AWS ElastiCache: reboot node
aws elasticache reboot-cache-cluster --cache-cluster-id my-cache \
  --cache-node-ids-to-reboot 0001
```

### Cache is up but database is overwhelmed (thundering herd)

```bash
# Immediate: Scale database read replicas
aws rds create-db-instance-read-replica \
  --db-instance-identifier mydb-emergency-replica \
  --source-db-instance-identifier mydb

# Immediate: Add rate limiting at application level
# Reduce concurrent cache-miss database queries

# Application fix: Implement cache stampede protection
# Use a "lock" pattern: only one request fetches from DB, others wait
```

### Memory full — eviction happening

```bash
# Check eviction count
redis-cli -h cache.example.com info stats | grep "evicted_keys"

# Option A: Increase max memory
redis-cli -h cache.example.com config set maxmemory 4gb

# Option B: Change eviction policy
redis-cli -h cache.example.com config set maxmemory-policy allkeys-lru

# Option C: Clear low-priority keys
redis-cli -h cache.example.com --scan --pattern "low-priority:*" | \
  xargs redis-cli -h cache.example.com del
```

## Step 4: Verify recovery

```bash
# Check cache hit rate is recovering
watch -n 10 'redis-cli -h cache.example.com info stats | grep keyspace'

# Check database load is decreasing
# Monitor database CPU and connection count in dashboard

# Check application latency returning to normal
curl -o /dev/null -s -w "Total: %{time_total}s\n" https://api.example.com/health
```

## Prevention

1. Monitor cache hit rate (alert if <90%)
2. Set appropriate maxmemory with LRU eviction
3. Implement cache stampede protection in application code
4. Use Redis Cluster or ElastiCache Multi-AZ for high availability
5. Test: what happens to your app if the cache disappears entirely?

## Related

- `research/runbooks/database-outage.md` — If cache failure cascades to DB
- `research/runbooks/high-cpu-memory-disk.md` — Cache memory issues
- `research/runbooks/slow-api-debugging.md` — Cache miss causing slow APIs
