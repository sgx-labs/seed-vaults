---
title: "AWS — Common Failure Modes, Status Page, Support Tiers"
tags: [aws, cloud, failure-modes, support, EC2, RDS, ELB, S3, entity]
content_type: entity
domain: operations
pinned: true
---

# AWS — Current State

Amazon Web Services (AWS) is the primary cloud infrastructure provider. This entity note covers common failure modes and diagnostic commands for EC2 instances, RDS databases, ELB/ALB load balancers, and S3 storage. It includes AWS support tier comparison, CLI quick diagnostics for CloudWatch alarms, service quotas, and caller identity checks. Reference this during AWS outages, RDS failovers, and cloud infrastructure troubleshooting.

## Status and Support

| Resource | URL |
|----------|-----|
| Service Health Dashboard | https://health.aws.amazon.com/ |
| Personal Health Dashboard | AWS Console > Health Dashboard |
| Support Center | AWS Console > Support |
| Post-Incident Summaries | https://aws.amazon.com/premiumsupport/technology/pes/ |

## Support Tiers

| Tier | Response Time (P0) | Cost | Best For |
|------|-------------------|------|----------|
| Basic | No support | Free | Personal projects |
| Developer | 12 hours | $29/mo | Development |
| Business | 1 hour | $100/mo+ | Production workloads |
| Enterprise | 15 minutes | $15K/mo+ | Mission-critical |

## Common Failure Modes

### EC2

```bash
# Instance unreachable
aws ec2 describe-instance-status --instance-ids i-1234567890abcdef0

# Check system status checks
aws ec2 describe-instance-status --filters Name=instance-status.status,Values=impaired

# Force stop and start (NOT reboot — moves to new host)
aws ec2 stop-instances --instance-ids i-1234567890abcdef0
aws ec2 start-instances --instance-ids i-1234567890abcdef0
```

### RDS

```bash
# Check instance status
aws rds describe-db-instances --db-instance-identifier mydb \
  --query 'DBInstances[0].DBInstanceStatus'

# Check recent events
aws rds describe-events --source-identifier mydb \
  --source-type db-instance --duration 60

# Failover (Multi-AZ)
aws rds reboot-db-instance --db-instance-identifier mydb --force-failover

# Restore from snapshot
aws rds restore-db-instance-from-db-snapshot \
  --db-instance-identifier mydb-restored \
  --db-snapshot-identifier mydb-snapshot-2026-02-14
```

### ELB / ALB

```bash
# Check target health
aws elbv2 describe-target-health --target-group-arn TARGET_GROUP_ARN

# Check unhealthy targets
aws elbv2 describe-target-health --target-group-arn TARGET_GROUP_ARN \
  --query 'TargetHealthDescriptions[?TargetHealth.State!=`healthy`]'
```

### S3

```bash
# Check if bucket is accessible
aws s3 ls s3://your-bucket/ --max-items 1

# Check bucket policy
aws s3api get-bucket-policy --bucket your-bucket
```

## AWS CLI Quick Diagnostics

```bash
# Current caller identity (am I using the right account?)
aws sts get-caller-identity

# Check service quotas
aws service-quotas list-service-quotas --service-code ec2

# CloudWatch recent alarms
aws cloudwatch describe-alarms --state-value ALARM
```

## Related

- `research/infrastructure/controlled-failover.md` — AWS region/AZ failover
- `research/infrastructure/cloud-cost-management.md` — Cost optimization
- `research/runbooks/database-outage.md` — RDS-specific recovery steps

## Last Updated

February 2026
