---
title: "Controlled Failover Procedure"
tags: [failover, disaster-recovery, multi-region, high-availability, runbook, DNS, Route53]
content_type: research
domain: operations
---

# Controlled Failover Procedure

## The Question

How do I perform a controlled failover to a secondary region or availability zone?

## When To Use

- Primary region/AZ experiencing outage
- Planned maintenance requiring full region evacuation
- Disaster recovery drill (do this quarterly)
- Cloud provider status page shows degradation in your region

## Severity: P1 (P0 if primary is already down)

## Prerequisites

Before failover is possible, you must have:
- [ ] Secondary region/AZ with infrastructure provisioned
- [ ] Database replication to secondary region
- [ ] DNS with health checks (Route 53, Cloudflare)
- [ ] Documented list of services and their failover state

## Step 1: Assess whether failover is necessary (5 minutes)

```bash
# Check cloud provider status
# AWS: https://health.aws.amazon.com/
# GCP: https://status.cloud.google.com/
# Azure: https://status.azure.com/

# Check your services in primary region
for svc in api-server payment-service auth-service; do
  echo "$svc:"
  curl -o /dev/null -s -w "HTTP %{http_code} in %{time_total}s\n" \
    https://${svc}-primary.example.com/health
done

# Check database replication lag
psql -h db-primary.example.com -U admin -c "
SELECT client_addr, state,
  pg_wal_lsn_diff(pg_current_wal_lsn(), replay_lsn) AS lag_bytes
FROM pg_stat_replication;
"
```

**Decision:** Failover if primary has been down >15 minutes with no ETA from provider, or if the outage is expected to last >1 hour.

## Step 2: Announce the failover (2 minutes)

1. Open incident channel: #failover-YYYY-MM-DD
2. Announce: "Initiating failover from [primary] to [secondary]. ETA: 15 minutes."
3. Update status page: "We are performing a failover to restore service."

## Step 3: Database failover (5-10 minutes)

### AWS RDS Multi-AZ (automatic)

```bash
# Force failover (triggers automatic AZ switch)
aws rds reboot-db-instance --db-instance-identifier mydb --force-failover

# Wait for it to become available
aws rds wait db-instance-available --db-instance-identifier mydb

# Verify new AZ
aws rds describe-db-instances --db-instance-identifier mydb \
  --query 'DBInstances[0].AvailabilityZone'
```

### Manual replica promotion (cross-region)

```bash
# AWS RDS: Promote read replica to standalone
aws rds promote-read-replica --db-instance-identifier mydb-secondary

# Wait for promotion
aws rds wait db-instance-available --db-instance-identifier mydb-secondary

# Verify it is now writable
psql -h mydb-secondary.example.com -U admin -c "INSERT INTO health_check (ts) VALUES (NOW());"
```

**WARNING:** After promoting a replica, replication is broken. You will need to set up replication again after the primary recovers.

## Step 4: Switch application traffic (5 minutes)

### DNS-based failover (Route 53)

```bash
# If using Route 53 health checks, failover should be automatic
# Verify:
aws route53 get-health-check-status --health-check-id PRIMARY_HC_ID

# Manual DNS switch:
aws route53 change-resource-record-sets --hosted-zone-id ZONE_ID \
  --change-batch '{
    "Changes": [{
      "Action": "UPSERT",
      "ResourceRecordSet": {
        "Name": "api.example.com",
        "Type": "A",
        "TTL": 60,
        "ResourceRecords": [{"Value": "SECONDARY_IP"}]
      }
    }]
  }'
```

### Load balancer failover

```bash
# Remove primary targets from load balancer
aws elbv2 deregister-targets --target-group-arn PRIMARY_TG_ARN \
  --targets Id=PRIMARY_INSTANCE_ID

# Register secondary targets
aws elbv2 register-targets --target-group-arn PRIMARY_TG_ARN \
  --targets Id=SECONDARY_INSTANCE_ID
```

### Kubernetes: Switch context

```bash
# Switch to secondary cluster
kubectl config use-context secondary-cluster

# Verify services are running
kubectl get pods -n production
kubectl get svc -n production
```

## Step 5: Verify failover (5 minutes)

```bash
# Test all critical endpoints
for endpoint in /health /v1/status /v1/users/me; do
  echo "$endpoint:"
  curl -o /dev/null -s -w "HTTP %{http_code} in %{time_total}s\n" \
    "https://api.example.com${endpoint}"
done

# Verify DNS is resolving to secondary
dig api.example.com +short

# Check application logs for errors
kubectl logs -n production deploy/api-server --since=5m | grep -i error | head -10

# Update status page
# "Service has been restored via failover to secondary region."
```

## Step 6: Failback (when primary recovers)

1. Verify primary region is healthy
2. Re-establish database replication from secondary to primary
3. Wait for replication to catch up (zero lag)
4. Switch traffic back to primary using same DNS/LB process
5. Verify primary is serving correctly
6. Demote secondary back to replica

## Related

- `research/runbooks/database-outage.md` — Database recovery
- `research/incidents/data-loss-response.md` — Data recovery
- `entities/aws.md` — AWS-specific failover commands
- `hub/infrastructure.md` — Infrastructure overview
