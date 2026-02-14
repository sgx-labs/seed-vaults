---
title: "Security Incident Response Runbook"
tags: [security, incident-response, breach, runbook, P0]
content_type: research
domain: operations
---

# Security Incident Response

## The Question

What are the steps for responding to a security incident?

## Severity

Always starts as **P0** until proven otherwise. Do not downgrade until you understand the scope.

## Step 1: Confirm the incident (5 minutes)

```bash
# Check for unauthorized access in auth logs
grep -i "unauthorized\|forbidden\|denied" /var/log/auth.log | tail -50

# Check for unusual SSH sessions
who -a
last -20

# Check for unexpected processes
ps aux --sort=-%cpu | head -20
ss -tlnp  # Check listening ports

# AWS: Check recent API activity
aws cloudtrail lookup-events --max-results 20 \
  --lookup-attributes AttributeKey=EventName,AttributeValue=ConsoleLogin
```

**If confirmed:** Proceed to Step 2 immediately. Do NOT try to fix it alone.

## Step 2: Activate incident response (2 minutes)

1. Page security team + engineering lead + VP Engineering
2. Open a dedicated incident channel (e.g., #incident-sec-YYYY-MM-DD)
3. Assign incident commander
4. Start a shared document for timeline tracking

**Do NOT:**
- Announce the breach publicly yet
- Shut down systems without IC approval (may destroy forensic evidence)
- Attempt to "hack back" or engage the attacker

## Step 3: Contain the threat (15 minutes)

```bash
# Revoke compromised credentials immediately
aws iam update-access-key --access-key-id AKIAEXAMPLE \
  --status Inactive --user-name compromised-user

# Disable compromised user accounts
aws iam update-login-profile --user-name compromised-user --no-password-reset-required
aws iam put-user-policy --user-name compromised-user \
  --policy-name DenyAll --policy-document '{"Version":"2012-10-17","Statement":[{"Effect":"Deny","Action":"*","Resource":"*"}]}'

# Block suspicious IPs at network level
# Firewall/security group update
aws ec2 revoke-security-group-ingress --group-id sg-12345678 \
  --protocol tcp --port 22 --cidr 203.0.113.0/24

# Isolate compromised instances (move to quarantine security group)
aws ec2 modify-instance-attribute --instance-id i-1234567890abcdef0 \
  --groups sg-quarantine

# Kubernetes: isolate compromised pod
kubectl label pod COMPROMISED_POD quarantine=true -n production
kubectl apply -f - <<EOF
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: quarantine
  namespace: production
spec:
  podSelector:
    matchLabels:
      quarantine: "true"
  policyTypes: ["Ingress", "Egress"]
EOF
```

## Step 4: Assess scope (30 minutes)

```bash
# What data was accessed?
# Check database audit logs
psql -h db.example.com -U admin -c "SELECT * FROM audit_log WHERE timestamp > NOW() - INTERVAL '24 hours' ORDER BY timestamp DESC LIMIT 100;"

# Check CloudTrail for data exfiltration
aws cloudtrail lookup-events --max-results 50 \
  --lookup-attributes AttributeKey=EventName,AttributeValue=GetObject

# Check for lateral movement
aws cloudtrail lookup-events --max-results 50 \
  --lookup-attributes AttributeKey=EventName,AttributeValue=AssumeRole
```

## Step 5: Eradicate and recover

1. Rotate ALL credentials (see `research/incidents/credential-rotation.md`)
2. Patch the vulnerability that was exploited
3. Restore affected systems from known-good backups
4. Verify integrity of restored data
5. Re-enable services with enhanced monitoring

## Step 6: Post-incident

1. Preserve all logs and forensic evidence
2. Determine notification requirements (GDPR, CCPA, PCI-DSS)
3. Notify legal/compliance team
4. Conduct post-mortem within 48 hours
5. Update runbooks with lessons learned

## Related

- `research/incidents/credential-rotation.md` — Credential rotation procedure
- `research/incidents/incident-classification.md` — Severity classification
- `templates/incident-report.md` — Incident documentation template
