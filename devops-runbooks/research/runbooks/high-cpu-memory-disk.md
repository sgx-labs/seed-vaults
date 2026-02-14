---
title: "High CPU / Memory / Disk Runbook"
tags: [CPU, memory, disk, OOM, resource-exhaustion, runbook, P1]
content_type: research
domain: operations
---

# High CPU / Memory / Disk Runbook

## The Question

What do I do when CPU, memory, or disk is maxed out?

## Symptoms

- CPU alerts: sustained >90% utilization
- Memory alerts: OOM kills, swap usage, application crashes
- Disk alerts: filesystem >85% full
- User impact: slow responses, timeouts, service unavailable

## Severity: P1 (P0 if causing outage)

## HIGH CPU

### Step 1: Identify the process

```bash
# Top processes by CPU
top -bn1 -o %CPU | head -20

# More detail with ps
ps aux --sort=-%cpu | head -15

# If it is a Java/Node/Python app, get a thread dump
# Java:
jstack $(pgrep java) > /tmp/thread-dump.txt

# Node.js: send SIGUSR1 for inspector
kill -USR1 $(pgrep node)
```

### Step 2: Kubernetes-specific

```bash
# Find pods consuming most CPU
kubectl top pods -n production --sort-by=cpu | head -10

# Check if CPU limits are being hit (throttling)
kubectl describe pod HIGH_CPU_POD -n production | grep -A3 "Limits\|Requests"

# Check container metrics
kubectl exec -n production HIGH_CPU_POD -- cat /sys/fs/cgroup/cpu/cpu.stat
```

### Step 3: Mitigate

```bash
# Option A: Scale horizontally (add replicas)
kubectl scale deployment/api-server -n production --replicas=8

# Option B: Restart the offending process (if stuck in a loop)
kubectl rollout restart deployment/api-server -n production

# Option C: Kill a specific runaway process
kill -15 PID  # graceful
kill -9 PID   # if graceful fails after 30 seconds
```

## HIGH MEMORY

### Step 1: Identify the leak

```bash
# Top processes by memory
ps aux --sort=-%mem | head -15

# Check for OOM kills
dmesg | grep -i "oom\|killed" | tail -20
journalctl -k | grep -i oom | tail -20

# Kubernetes: check for OOMKilled pods
kubectl get pods -n production -o json | \
  jq -r '.items[] | select(.status.containerStatuses[]?.lastState.terminated.reason == "OOMKilled") | .metadata.name'
```

### Step 2: Kubernetes-specific

```bash
# Pods sorted by memory usage
kubectl top pods -n production --sort-by=memory | head -10

# Check memory limits
kubectl describe pod HIGH_MEM_POD -n production | grep -A3 "Limits"

# Check if pod was OOM killed
kubectl describe pod HIGH_MEM_POD -n production | grep -A5 "Last State"
```

### Step 3: Mitigate

```bash
# Option A: Increase memory limits (temporary)
kubectl set resources deployment/api-server -n production \
  --limits=memory=2Gi --requests=memory=1Gi

# Option B: Restart pod (clears leaked memory)
kubectl delete pod HIGH_MEM_POD -n production

# Option C: Scale up node pool (if cluster is out of resources)
# AWS EKS:
aws eks update-nodegroup-config --cluster-name my-cluster \
  --nodegroup-name workers --scaling-config minSize=3,maxSize=10,desiredSize=6
```

## HIGH DISK

### Step 1: Find what is using space

```bash
# Overall disk usage
df -h

# Largest directories
du -sh /* 2>/dev/null | sort -rh | head -10
du -sh /var/log/* | sort -rh | head -10

# Largest files
find / -type f -size +100M -exec ls -lh {} \; 2>/dev/null | sort -k5 -rh | head -10

# Check for deleted files still held by processes
lsof +L1 | head -20
```

### Step 2: Clean up

```bash
# Rotate and compress logs
logrotate -f /etc/logrotate.conf

# Clear old journal logs (keep last 3 days)
journalctl --vacuum-time=3d

# Clean Docker resources
docker system prune -a --volumes -f

# Clean old container images (Kubernetes nodes)
crictl rmi --prune

# Clear package manager cache
apt-get clean    # Debian/Ubuntu
yum clean all    # CentOS/RHEL

# Delete old temporary files
find /tmp -type f -mtime +7 -delete
```

### Step 3: Expand disk (if cleanup is not enough)

```bash
# AWS EBS: Expand volume
aws ec2 modify-volume --volume-id vol-12345678 --size 200

# Resize filesystem (after volume modification completes)
sudo growpart /dev/xvda 1       # grow partition
sudo resize2fs /dev/xvda1       # ext4
sudo xfs_growfs /               # xfs
```

## Post-Resolution

1. Set up alerts for 80% thresholds (warning) and 90% (critical)
2. Investigate root cause (memory leak? log rotation missing? unbounded cache?)
3. Add resource limits to all containers
4. Schedule capacity planning review

## Related

- `research/runbooks/database-outage.md` — Disk full can crash databases
- `research/infrastructure/scaling-procedures.md` — Scaling up resources
- `research/monitoring/alert-fatigue.md` — Setting appropriate thresholds
- `entities/kubernetes.md` — K8s resource management
