---
title: "DNS Outage Runbook"
tags: [DNS, outage, resolution, networking, runbook, P0]
content_type: research
domain: operations
---

# DNS Outage Runbook

## The Question

What do I do when DNS is not resolving?

## Symptoms

- Users reporting "site can't be reached" or "DNS_PROBE_FINISHED_NXDOMAIN"
- Applications failing with "Name or service not known"
- curl/wget failing on domain resolution but working with IP addresses
- Health checks failing on hostname-based endpoints

## Severity: P0

DNS failure affects all users who cannot resolve your domain. Immediate response required.

## Step 1: Confirm DNS failure (2 minutes)

```bash
# Test resolution from your machine
dig example.com +short
dig example.com @8.8.8.8 +short      # Google DNS
dig example.com @1.1.1.1 +short      # Cloudflare DNS

# Test with nslookup
nslookup example.com
nslookup example.com 8.8.8.8

# Check specific record types
dig example.com A +short              # IPv4
dig example.com AAAA +short           # IPv6
dig example.com CNAME +short          # CNAME
dig example.com MX +short             # Mail
dig example.com NS +short             # Nameservers

# Check if the site works by IP (proves it's DNS, not the service)
curl -H "Host: example.com" http://YOUR_SERVER_IP/health

# Check DNS propagation globally
# Use: https://www.whatsmydns.net/
```

**Key question:** Does it resolve from some DNS servers but not others? If yes, it is a propagation issue. If no resolution from any server, it is a configuration issue.

## Step 2: Identify the cause

### Check nameserver delegation

```bash
# What nameservers are authoritative?
dig example.com NS +short

# Are the nameservers responding?
dig example.com @ns1.your-dns-provider.com +short

# Check SOA record (zone authority)
dig example.com SOA
```

### Check DNS provider status

```bash
# Route 53
aws route53 get-hosted-zone --id HOSTED_ZONE_ID
aws route53 list-resource-record-sets --hosted-zone-id HOSTED_ZONE_ID \
  --query "ResourceRecordSets[?Name=='example.com.']"

# Check Route 53 health checks
aws route53 list-health-checks
aws route53 get-health-check-status --health-check-id HC_ID
```

### Check for recent changes

```bash
# Route 53: Check change history
aws route53 list-changes-by-hosted-zone --hosted-zone-id HOSTED_ZONE_ID \
  --max-items 10

# Check domain registration (is the domain expired?)
whois example.com | grep -i "expir"
```

## Step 3: Fix based on cause

### Cause: DNS record deleted or misconfigured

```bash
# Route 53: Recreate the A record
aws route53 change-resource-record-sets --hosted-zone-id HOSTED_ZONE_ID \
  --change-batch '{
    "Changes": [{
      "Action": "UPSERT",
      "ResourceRecordSet": {
        "Name": "example.com",
        "Type": "A",
        "TTL": 300,
        "ResourceRecords": [{"Value": "YOUR_SERVER_IP"}]
      }
    }]
  }'
```

### Cause: Nameserver delegation broken

Contact your domain registrar to verify nameserver settings match your DNS provider.

### Cause: DNS provider outage

```bash
# Temporary: Add static hosts entry on critical servers
echo "YOUR_SERVER_IP example.com" >> /etc/hosts

# Temporary: Point to a secondary DNS provider if configured
# This requires pre-planning with multiple NS records
```

### Cause: Domain expired

Renew immediately through your registrar. Recovery can take 24-72 hours for propagation.

## Step 4: Verify recovery

```bash
# Test resolution from multiple sources
for dns in 8.8.8.8 1.1.1.1 9.9.9.9; do
  echo "Testing $dns:"
  dig example.com @$dns +short
done

# Test end-to-end
curl -o /dev/null -s -w "HTTP %{http_code} in %{time_total}s\n" https://example.com/health

# Monitor propagation
watch -n 30 'dig example.com +short'
```

## Prevention

1. Set domain auto-renewal to ON
2. Monitor DNS resolution with synthetic checks
3. Use low TTLs (300s) on critical records so changes propagate fast
4. Keep a documented list of all DNS records
5. Enable DNSSEC if your provider supports it

## Related

- `research/monitoring/synthetic-monitoring.md` — DNS monitoring setup
- `research/incidents/incident-classification.md` — Severity classification
- `hub/infrastructure.md` — Infrastructure overview
