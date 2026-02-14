---
title: "Scaling Procedures — Horizontal and Vertical"
tags: [scaling, autoscaling, horizontal, vertical, capacity, infrastructure, HPA, EKS]
content_type: research
domain: operations
---

# Scaling Procedures

## The Question

What do I do when auto-scaling fails or I need to manually scale?

## When To Scale

| Signal | Action |
|--------|--------|
| CPU sustained >80% across all pods | Scale out (add replicas) |
| Memory sustained >80% across all pods | Scale out or scale up (more memory) |
| Request queue growing | Scale out (add replicas) |
| Response time increasing under load | Scale out (add replicas) |
| Pod pending (insufficient resources) | Scale up (bigger nodes) |
| All nodes at capacity | Add nodes to cluster |

## Horizontal Scaling (Add More Instances)

### Kubernetes: Scale deployment

```bash
# Scale to specific replica count
kubectl scale deployment/api-server -n production --replicas=10

# Check current HPA (Horizontal Pod Autoscaler)
kubectl get hpa -n production

# Describe HPA to see scaling decisions
kubectl describe hpa api-server -n production

# Create or update HPA
kubectl autoscale deployment/api-server -n production \
  --min=3 --max=20 --cpu-percent=70

# If HPA is not scaling: check metrics-server
kubectl top pods -n production  # Should return values
kubectl get apiservice v1beta1.metrics.k8s.io  # Should be Available
```

### AWS Auto Scaling Group

```bash
# Check current capacity
aws autoscaling describe-auto-scaling-groups \
  --auto-scaling-group-names my-asg \
  --query 'AutoScalingGroups[0].[MinSize,MaxSize,DesiredCapacity]'

# Scale out manually
aws autoscaling set-desired-capacity \
  --auto-scaling-group-name my-asg \
  --desired-capacity 10

# Update ASG limits (if hitting max)
aws autoscaling update-auto-scaling-group \
  --auto-scaling-group-name my-asg \
  --min-size 3 --max-size 20 --desired-capacity 10
```

### AWS EKS: Scale node group

```bash
# Check current node count
kubectl get nodes | wc -l

# Scale EKS managed node group
aws eks update-nodegroup-config --cluster-name my-cluster \
  --nodegroup-name workers \
  --scaling-config minSize=3,maxSize=15,desiredSize=8

# Verify new nodes join
watch kubectl get nodes
```

## Vertical Scaling (Bigger Instances)

### Kubernetes: Increase resource limits

```bash
# Update deployment resource limits
kubectl set resources deployment/api-server -n production \
  --limits=cpu=2,memory=4Gi \
  --requests=cpu=1,memory=2Gi

# This triggers a rolling restart with new limits
kubectl rollout status deployment/api-server -n production
```

### AWS: Resize an instance

```bash
# Stop the instance
aws ec2 stop-instances --instance-ids i-1234567890abcdef0
aws ec2 wait instance-stopped --instance-ids i-1234567890abcdef0

# Change instance type
aws ec2 modify-instance-attribute --instance-id i-1234567890abcdef0 \
  --instance-type '{"Value": "m5.2xlarge"}'

# Start the instance
aws ec2 start-instances --instance-ids i-1234567890abcdef0
aws ec2 wait instance-running --instance-ids i-1234567890abcdef0
```

**WARNING:** Vertical scaling of non-K8s instances requires downtime. Use horizontal scaling for zero-downtime scaling.

## Database Scaling

```bash
# AWS RDS: Add read replicas
aws rds create-db-instance-read-replica \
  --db-instance-identifier mydb-replica-2 \
  --source-db-instance-identifier mydb

# AWS RDS: Vertical scale (requires maintenance window or immediate apply)
aws rds modify-db-instance --db-instance-identifier mydb \
  --db-instance-class db.r5.2xlarge \
  --apply-immediately

# ElastiCache: Add replicas
aws elasticache increase-replica-count \
  --replication-group-id my-cache \
  --new-replica-count 3 \
  --apply-immediately
```

## Troubleshooting Auto-Scaling

```bash
# HPA not scaling up?
# Check 1: Are metrics available?
kubectl top pods -n production

# Check 2: Is HPA seeing the metrics?
kubectl describe hpa api-server -n production
# Look for "unable to fetch metrics" errors

# Check 3: Have you hit the max replicas?
kubectl get hpa -n production
# If REPLICAS == MAXPODS, increase max

# Check 4: Is there room on nodes for new pods?
kubectl describe nodes | grep -A5 "Allocated resources"
```

## Related

- `research/runbooks/high-cpu-memory-disk.md` — When to scale
- `research/infrastructure/kubernetes-failure-modes.md` — Pod pending issues
- `research/infrastructure/cloud-cost-management.md` — Cost of scaling
- `entities/aws.md` — AWS scaling commands
