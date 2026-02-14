---
title: "Cloud Cost Management"
tags: [cost, cloud, optimization, FinOps, budget, infrastructure]
content_type: research
domain: operations
---

# Cloud Cost Management

## The Question

How do I manage cloud costs without sacrificing reliability?

## Monthly Cost Review Checklist

```bash
# AWS: Check current month costs
aws ce get-cost-and-usage \
  --time-period Start=$(date +%Y-%m-01),End=$(date +%Y-%m-%d) \
  --granularity MONTHLY \
  --metrics "UnblendedCost" \
  --group-by Type=DIMENSION,Key=SERVICE

# AWS: Find the top cost drivers
aws ce get-cost-and-usage \
  --time-period Start=$(date -d "30 days ago" +%Y-%m-%d),End=$(date +%Y-%m-%d) \
  --granularity DAILY \
  --metrics "UnblendedCost" \
  --group-by Type=DIMENSION,Key=SERVICE \
  --query 'ResultsByTime[-1].Groups | sort_by(@, &Metrics.UnblendedCost.Amount) | [-5:]'
```

## Quick Wins (Implement Today)

### 1. Find idle resources

```bash
# AWS: Find unattached EBS volumes
aws ec2 describe-volumes --filters Name=status,Values=available \
  --query 'Volumes[].[VolumeId,Size,CreateTime]' --output table

# AWS: Find unused Elastic IPs
aws ec2 describe-addresses --query 'Addresses[?AssociationId==null].[PublicIp,AllocationId]'

# AWS: Find old snapshots (>90 days)
aws ec2 describe-snapshots --owner-ids self \
  --query "Snapshots[?StartTime<='$(date -d '90 days ago' +%Y-%m-%d)'].[SnapshotId,VolumeSize,StartTime]" \
  --output table
```

### 2. Right-size instances

```bash
# AWS: Get right-sizing recommendations
aws ce get-rightsizing-recommendation \
  --service AmazonEC2 \
  --configuration '{"RecommendationTarget":"SAME_INSTANCE_FAMILY","BenefitsConsidered":true}'

# Check current instance utilization
# Low CPU (<20% average) = overprovisioned
aws cloudwatch get-metric-statistics \
  --namespace AWS/EC2 --metric-name CPUUtilization \
  --dimensions Name=InstanceId,Value=i-1234567890abcdef0 \
  --start-time $(date -d '7 days ago' -u +%Y-%m-%dT%H:%M:%S) \
  --end-time $(date -u +%Y-%m-%dT%H:%M:%S) \
  --period 86400 --statistics Average
```

### 3. Use reserved capacity for stable workloads

| Option | Savings | Commitment | Best For |
|--------|---------|------------|----------|
| On-Demand | 0% | None | Spiky, unpredictable workloads |
| Reserved (1yr) | 30-40% | 1 year | Stable production workloads |
| Reserved (3yr) | 50-60% | 3 years | Core infrastructure |
| Spot/Preemptible | 60-90% | None (can be reclaimed) | Batch jobs, CI/CD, dev/test |
| Savings Plans | 30-50% | 1-3 years | Flexible across services |

## Cost Alerting

```bash
# AWS: Set a budget alert
aws budgets create-budget --account-id ACCOUNT_ID \
  --budget '{
    "BudgetName": "MonthlyBudget",
    "BudgetLimit": {"Amount": "5000", "Unit": "USD"},
    "TimeUnit": "MONTHLY",
    "BudgetType": "COST"
  }' \
  --notifications-with-subscribers '[{
    "Notification": {
      "NotificationType": "ACTUAL",
      "ComparisonOperator": "GREATER_THAN",
      "Threshold": 80,
      "ThresholdType": "PERCENTAGE"
    },
    "Subscribers": [{"SubscriptionType": "EMAIL", "Address": "ops@example.com"}]
  }]'
```

## Kubernetes Cost Optimization

```bash
# Find overprovisioned pods (requests >> actual usage)
kubectl top pods -n production --sort-by=cpu
# Compare with requested resources:
kubectl get pods -n production -o custom-columns="NAME:.metadata.name,CPU_REQ:.spec.containers[0].resources.requests.cpu,MEM_REQ:.spec.containers[0].resources.requests.memory"

# Set resource requests to match actual usage (with 20% headroom)
# Use tools like Goldilocks or VPA recommendations
```

## Related

- `research/infrastructure/scaling-procedures.md` — Right-size before scaling
- `entities/aws.md` — AWS cost commands
- `hub/infrastructure.md` — Infrastructure overview
