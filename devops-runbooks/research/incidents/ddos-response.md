---
title: "DDoS Attack Response Runbook"
tags: [ddos, attack, mitigation, security, runbook, P0]
content_type: research
domain: operations
---

# DDoS Attack Response

## The Question

How do I respond to a DDoS attack?

## Symptoms

- Sudden traffic spike (10x-100x normal)
- High latency or timeouts for all users
- Load balancer connection limits hit
- Bandwidth saturation on network interfaces
- Origin servers unresponsive despite being healthy

## Severity: P0

DDoS affects all users. Classify as P0 immediately.

## Step 1: Confirm it is a DDoS (3 minutes)

```bash
# Check current connection count
ss -s
netstat -an | wc -l

# Check requests per second (nginx)
tail -1000 /var/log/nginx/access.log | awk '{print $1}' | sort | uniq -c | sort -rn | head -20

# Check top IPs hitting your service
awk '{print $1}' /var/log/nginx/access.log | sort | uniq -c | sort -rn | head -20

# Check for traffic pattern (same URL, same user-agent)
awk '{print $7}' /var/log/nginx/access.log | sort | uniq -c | sort -rn | head -10

# AWS: Check network flow logs
aws ec2 describe-flow-logs --filter Name=resource-id,Values=vpc-12345678
```

**Is it a DDoS?** If traffic is from many diverse IPs hitting your service with abnormal patterns, yes. If it is legitimate traffic (e.g., you went viral), scale up instead.

## Step 2: Activate DDoS protection (5 minutes)

### If using Cloudflare:

```bash
# Enable "Under Attack" mode via API
curl -X PATCH "https://api.cloudflare.com/client/v4/zones/ZONE_ID/settings/security_level" \
  -H "Authorization: Bearer CF_API_TOKEN" \
  -H "Content-Type: application/json" \
  --data '{"value":"under_attack"}'

# Create rate limiting rule
curl -X POST "https://api.cloudflare.com/client/v4/zones/ZONE_ID/rate_limits" \
  -H "Authorization: Bearer CF_API_TOKEN" \
  -H "Content-Type: application/json" \
  --data '{
    "threshold": 100,
    "period": 60,
    "action": {"mode": "challenge"},
    "match": {"request": {"url_pattern": "*"}}
  }'
```

### If using AWS:

```bash
# Enable AWS Shield Advanced (if subscribed)
# Shield Standard is automatic for CloudFront, ALB, Route 53

# Create WAF rate-based rule
aws wafv2 create-rate-based-statement --name "DDOSBlock" \
  --scope REGIONAL --limit 2000 --aggregate-key-type IP

# Block specific countries (if attack source is identifiable)
# Use AWS WAF geo-match statement

# Scale up ALB to handle additional traffic
# ALB scales automatically, but pre-warm if needed via AWS Support
```

### At the origin (if no CDN):

```bash
# Rate limit with iptables
iptables -A INPUT -p tcp --dport 80 -m connlimit --connlimit-above 50 -j DROP
iptables -A INPUT -p tcp --dport 443 -m connlimit --connlimit-above 50 -j DROP

# Block top attacking IPs
for ip in $(awk '{print $1}' /var/log/nginx/access.log | sort | uniq -c | sort -rn | head -10 | awk '{print $2}'); do
  iptables -A INPUT -s $ip -j DROP
done

# Enable nginx rate limiting
# In nginx.conf:
# limit_req_zone $binary_remote_addr zone=ddos:10m rate=10r/s;
# limit_req zone=ddos burst=20 nodelay;
```

## Step 3: Monitor and adjust (ongoing)

```bash
# Watch traffic in real-time
tail -f /var/log/nginx/access.log | awk '{print $1}' | sort | uniq -c | sort -rn

# Check if mitigation is working
curl -o /dev/null -s -w "HTTP %{http_code} in %{time_total}s\n" https://your-service.example.com/health
```

## Step 4: Post-attack

1. Remove temporary IP blocks and rate limits
2. Disable "Under Attack" mode once traffic normalizes
3. Analyze attack patterns for future prevention
4. Consider upgrading DDoS protection tier
5. Document in post-mortem

## Related

- `research/incidents/incident-classification.md` — Severity classification
- `research/infrastructure/scaling-procedures.md` — Scaling under load
- `entities/aws.md` — AWS Shield and WAF details
