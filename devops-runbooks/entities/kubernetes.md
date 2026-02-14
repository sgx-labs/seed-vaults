---
title: "Kubernetes — Debugging, Common Issues, kubectl Patterns"
tags: [kubernetes, k8s, kubectl, debugging, pods, nodes, deployments, entity]
content_type: entity
domain: operations
pinned: true
---

# Kubernetes — Current State

Kubernetes (K8s) is the container orchestration platform for deploying, scaling, and operating containerized applications. This entity note provides essential kubectl commands for cluster health, pod debugging, deployment rollback, node management (cordon, drain, uncordon), and common pod failure states including CrashLoopBackOff, ImagePullBackOff, OOMKilled, Pending, and Evicted. Reference this during any Kubernetes-related incident.

## Essential kubectl Commands

```bash
# Cluster health
kubectl cluster-info
kubectl get nodes -o wide
kubectl top nodes

# Pod status (all namespaces)
kubectl get pods --all-namespaces -o wide
kubectl get pods -n NAMESPACE --sort-by='.status.containerStatuses[0].restartCount'

# Describe a failing pod
kubectl describe pod POD_NAME -n NAMESPACE

# Pod logs (current and previous crash)
kubectl logs POD_NAME -n NAMESPACE
kubectl logs POD_NAME -n NAMESPACE --previous

# Execute into a running pod
kubectl exec -it POD_NAME -n NAMESPACE -- /bin/sh

# Events (sorted by time)
kubectl get events -n NAMESPACE --sort-by='.lastTimestamp'

# Resource usage
kubectl top pods -n NAMESPACE --sort-by=memory
kubectl top pods -n NAMESPACE --sort-by=cpu
```

## Common Pod States and Fixes

| State | Meaning | First Step |
|-------|---------|------------|
| **CrashLoopBackOff** | Container keeps crashing | `kubectl logs POD --previous` |
| **ImagePullBackOff** | Cannot pull container image | Check image name, registry auth |
| **Pending** | Cannot be scheduled | `kubectl describe pod` — check events |
| **OOMKilled** | Out of memory (exit code 137) | Increase memory limits or fix leak |
| **Evicted** | Node ran out of resources | Check node capacity, clean up |
| **CreateContainerError** | Container config issue | Check configmaps, secrets, mounts |
| **Init:Error** | Init container failed | `kubectl logs POD -c INIT_CONTAINER` |

## Deployment Operations

```bash
# Check deployment status
kubectl rollout status deployment/DEPLOY_NAME -n NAMESPACE

# View rollout history
kubectl rollout history deployment/DEPLOY_NAME -n NAMESPACE

# Rollback to previous version
kubectl rollout undo deployment/DEPLOY_NAME -n NAMESPACE

# Rollback to specific revision
kubectl rollout undo deployment/DEPLOY_NAME -n NAMESPACE --to-revision=3

# Scale deployment
kubectl scale deployment/DEPLOY_NAME -n NAMESPACE --replicas=5

# Restart all pods in a deployment
kubectl rollout restart deployment/DEPLOY_NAME -n NAMESPACE
```

## Node Troubleshooting

```bash
# Cordon node (prevent new pods)
kubectl cordon NODE_NAME

# Drain node (evict pods safely)
kubectl drain NODE_NAME --ignore-daemonsets --delete-emptydir-data

# Uncordon node (allow scheduling again)
kubectl uncordon NODE_NAME

# Check node conditions
kubectl describe node NODE_NAME | grep -A5 Conditions
```

## Related

- `research/infrastructure/kubernetes-failure-modes.md` — Full K8s runbook
- `research/deployment/rollback-procedures.md` — Rollback strategies
- `research/runbooks/high-cpu-memory-disk.md` — Resource issues in pods

## Last Updated

February 2026
