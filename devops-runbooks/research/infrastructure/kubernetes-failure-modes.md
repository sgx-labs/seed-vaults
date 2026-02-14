---
title: "Kubernetes Failure Modes and Fixes"
tags: [kubernetes, k8s, failure, CrashLoopBackOff, OOMKilled, runbook]
content_type: research
domain: operations
---

# Kubernetes Failure Modes and Fixes

## The Question

What are the common Kubernetes failure modes and how do I fix them?

## Quick Diagnosis

```bash
# Get the full picture
kubectl get pods -n production -o wide
kubectl get events -n production --sort-by='.lastTimestamp' | tail -20
kubectl top nodes
kubectl top pods -n production
```

## Failure Mode: CrashLoopBackOff

**Meaning:** Container starts, crashes, restarts, crashes, repeat. Backoff delay increases each time.

```bash
# Step 1: Check logs from the crashed container
kubectl logs POD_NAME -n production --previous

# Step 2: Check pod events
kubectl describe pod POD_NAME -n production | grep -A10 Events

# Step 3: Check exit code
kubectl get pod POD_NAME -n production -o jsonpath='{.status.containerStatuses[0].lastState.terminated.exitCode}'
# Exit 1: Application error
# Exit 137: OOMKilled (see below)
# Exit 139: Segfault
```

**Common fixes:**
- Missing environment variable: Check configmaps/secrets are mounted
- Dependency not ready: Add init container or readiness probe
- Application bug: Check logs, fix code, redeploy

## Failure Mode: ImagePullBackOff

**Meaning:** Kubernetes cannot pull the container image.

```bash
# Step 1: Check the image name
kubectl get pod POD_NAME -n production -o jsonpath='{.spec.containers[0].image}'

# Step 2: Check events for details
kubectl describe pod POD_NAME -n production | grep -A5 "Failed\|Error\|pull"

# Step 3: Verify image exists
docker pull YOUR_REGISTRY/image:tag
```

**Common fixes:**
- Typo in image name or tag
- Image does not exist in registry
- Registry authentication expired: recreate `imagePullSecret`
- Registry rate limit (Docker Hub): use a mirror or authenticated pull

```bash
# Recreate registry credentials
kubectl delete secret regcred -n production
kubectl create secret docker-registry regcred -n production \
  --docker-server=YOUR_REGISTRY \
  --docker-username=USER \
  --docker-password=PASSWORD
```

## Failure Mode: OOMKilled (Exit Code 137)

**Meaning:** Container exceeded its memory limit and was killed by the kernel.

```bash
# Step 1: Check current memory usage vs limits
kubectl describe pod POD_NAME -n production | grep -A3 "Limits\|Requests"
kubectl top pod POD_NAME -n production

# Step 2: Check if OOMKilled
kubectl get pod POD_NAME -n production -o jsonpath='{.status.containerStatuses[0].lastState.terminated.reason}'
```

**Fixes:**
```bash
# Option A: Increase memory limit
kubectl set resources deployment/SERVICE -n production \
  --limits=memory=2Gi --requests=memory=1Gi

# Option B: If it is a memory leak, restart and investigate
kubectl delete pod POD_NAME -n production
# Then profile the application to find the leak
```

## Failure Mode: Pod Pending

**Meaning:** Pod cannot be scheduled to any node.

```bash
# Step 1: Check why
kubectl describe pod POD_NAME -n production | grep -A5 Events

# Common reasons:
# "Insufficient cpu" or "Insufficient memory" → cluster needs more nodes
# "node(s) had taints" → node taints don't match pod tolerations
# "persistentvolumeclaim not found" → PVC doesn't exist or isn't bound
```

**Fixes:**
```bash
# Not enough resources: scale node pool
# AWS EKS:
aws eks update-nodegroup-config --cluster-name my-cluster \
  --nodegroup-name workers --scaling-config minSize=3,maxSize=10,desiredSize=6

# PVC issue: check PVC status
kubectl get pvc -n production
kubectl describe pvc PVC_NAME -n production
```

## Failure Mode: Node NotReady

**Meaning:** A node has stopped communicating with the control plane.

```bash
# Step 1: Check node status
kubectl get nodes
kubectl describe node NODE_NAME | grep -A10 Conditions

# Step 2: Check node resources
kubectl describe node NODE_NAME | grep -A5 "Allocated resources"

# Step 3: If node is unrecoverable
kubectl cordon NODE_NAME              # prevent new pods
kubectl drain NODE_NAME --ignore-daemonsets --delete-emptydir-data  # move pods
# Then terminate/replace the node
```

## Failure Mode: Service Not Reachable

**Meaning:** Pods are running but cannot be reached via the Service.

```bash
# Step 1: Verify service exists and has endpoints
kubectl get svc SERVICE_NAME -n production
kubectl get endpoints SERVICE_NAME -n production

# Step 2: Check if selector matches pod labels
kubectl get svc SERVICE_NAME -n production -o jsonpath='{.spec.selector}'
kubectl get pods -n production -l app=SERVICE_NAME

# Step 3: Test from within the cluster
kubectl run test-curl --rm -it --image=curlimages/curl -- \
  curl -v http://SERVICE_NAME.production.svc.cluster.local:PORT/health
```

## Related

- `entities/kubernetes.md` — kubectl command reference
- `research/runbooks/high-cpu-memory-disk.md` — Resource issues in pods
- `research/deployment/rollback-procedures.md` — Rolling back deployments
- `hub/infrastructure.md` — Infrastructure overview
