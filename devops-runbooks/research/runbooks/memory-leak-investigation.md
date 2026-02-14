---
title: "Memory Leak Investigation Runbook"
tags: [memory, leak, OOM, profiling, debugging, runbook, heap-dump, garbage-collection]
content_type: research
domain: operations
---

# Memory Leak Investigation

## The Question

How do I find and fix a memory leak in production?

## Symptoms

- Memory usage grows steadily over hours/days (sawtooth pattern with restarts)
- OOMKilled events increasing
- Application performance degrades over time, then recovers after restart
- Garbage collection pauses getting longer

## Severity: P2 (P1 if causing OOM crashes)

## Step 1: Confirm it is a leak (5 minutes)

```bash
# Check memory trend over time
# Prometheus:
# container_memory_working_set_bytes{pod=~"api-server.*"} over 24h
# If it trends upward with no plateau, it is a leak

# Kubernetes: Check current usage vs limits
kubectl top pods -n production -l app=api-server
kubectl describe pod POD_NAME -n production | grep -A3 "Limits"

# Check restart count (frequent OOM restarts)
kubectl get pods -n production -l app=api-server \
  -o custom-columns="NAME:.metadata.name,RESTARTS:.status.containerStatuses[0].restartCount,AGE:.metadata.creationTimestamp"

# Check for OOMKilled events
kubectl get events -n production | grep OOMKill
```

## Step 2: Identify the source

### Node.js

```bash
# Enable heap snapshots
kubectl exec -n production POD_NAME -- \
  node --inspect=0.0.0.0:9229 --expose-gc

# Generate heap snapshot
kubectl exec -n production POD_NAME -- \
  kill -USR2 1  # Triggers heap dump (Node.js 12+)

# Check heap statistics
kubectl exec -n production POD_NAME -- \
  node -e "console.log(process.memoryUsage())"

# Key metrics:
# heapUsed: JS objects
# heapTotal: V8 heap allocated
# external: C++ objects bound to JS
# rss: Total resident memory
```

### Java

```bash
# Generate heap dump
kubectl exec -n production POD_NAME -- \
  jmap -dump:format=b,file=/tmp/heapdump.hprof $(pgrep java)

# Copy heap dump locally for analysis
kubectl cp production/POD_NAME:/tmp/heapdump.hprof ./heapdump.hprof

# Check heap summary
kubectl exec -n production POD_NAME -- \
  jmap -heap $(pgrep java)

# Check GC activity
kubectl exec -n production POD_NAME -- \
  jstat -gcutil $(pgrep java) 1000 10
# Watch for Old Gen (O) trending toward 100%
```

### Python

```bash
# Add memory profiling to the application
# Use tracemalloc (built-in):
kubectl exec -n production POD_NAME -- python3 -c "
import tracemalloc
tracemalloc.start()
# ... your app code ...
snapshot = tracemalloc.take_snapshot()
for stat in snapshot.statistics('lineno')[:10]:
    print(stat)
"

# Check process memory from OS level
kubectl exec -n production POD_NAME -- \
  cat /proc/1/status | grep -i "vmrss\|vmsize"
```

### Generic (any language)

```bash
# Monitor memory growth in real time
kubectl exec -n production POD_NAME -- \
  watch -n 5 'cat /proc/1/status | grep VmRSS'

# Check for file descriptor leaks (can look like memory leaks)
kubectl exec -n production POD_NAME -- ls -la /proc/1/fd | wc -l
# If growing: connection or file handle leak
```

## Step 3: Mitigate (while investigating)

```bash
# Option A: Scheduled restart (band-aid)
# Add a CronJob that restarts the deployment daily
kubectl rollout restart deployment/api-server -n production

# Option B: Increase memory limits (buys time)
kubectl set resources deployment/api-server -n production \
  --limits=memory=4Gi --requests=memory=2Gi

# Option C: Reduce traffic to affected pods
kubectl scale deployment/api-server -n production --replicas=10
# More pods = each pod handles less = slower leak per pod
```

## Common Causes

| Cause | Language | Fix |
|-------|----------|-----|
| Unbounded cache | All | Add max size and TTL to caches |
| Event listener not removed | Node.js | Remove listeners on cleanup |
| Database connection leak | All | Use connection pooling with max limit |
| Circular references | Python | Use weakref for back-references |
| Static collections growing | Java | Use WeakHashMap or bounded collections |
| Goroutine leak | Go | Ensure all goroutines have exit conditions |

## Related

- `research/runbooks/high-cpu-memory-disk.md` — Immediate memory response
- `research/infrastructure/kubernetes-failure-modes.md` — OOMKilled pods
- `research/runbooks/slow-api-debugging.md` — Memory leak causing slowness
