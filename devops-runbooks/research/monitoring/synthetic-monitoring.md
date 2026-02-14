---
title: "Synthetic Monitoring Setup"
tags: [synthetic, monitoring, uptime, health-checks, probes]
content_type: research
domain: operations
---

# Synthetic Monitoring Setup

## The Question

How do I set up synthetic monitoring to catch problems before users do?

## What Is Synthetic Monitoring?

Synthetic monitoring runs automated tests against your services from external locations on a schedule. It simulates user behavior and catches outages before real users hit them.

**Difference from real user monitoring (RUM):**
- RUM: Measures actual user experience (reactive)
- Synthetics: Simulates user actions on a schedule (proactive)

## Types of Synthetic Checks

| Type | Checks | Frequency |
|------|--------|-----------|
| **HTTP/API** | Endpoint returns 200, latency < threshold | Every 1-5 min |
| **TCP/Port** | Port is open and accepting connections | Every 1-5 min |
| **DNS** | Domain resolves to expected IP | Every 5 min |
| **SSL/TLS** | Certificate is valid, not expiring soon | Every hour |
| **Browser** | Page loads, critical elements render | Every 5-15 min |
| **Multi-step** | Login flow, checkout flow, search | Every 15-30 min |

## Step 1: Define critical paths

List the user journeys that generate revenue or define your core product:

```
1. Homepage loads                    → HTTP check
2. User can log in                   → Multi-step check
3. Search returns results            → API check
4. Checkout completes                → Multi-step check
5. API responds to authenticated requests → API check
6. DNS resolves correctly            → DNS check
7. SSL certificate is valid          → TLS check
```

## Step 2: Set up HTTP/API checks

### Using curl (cron-based, DIY)

```bash
#!/bin/bash
# /usr/local/bin/health-check.sh
# Run via cron: */2 * * * * /usr/local/bin/health-check.sh

ENDPOINTS=(
  "https://api.example.com/health"
  "https://www.example.com/"
  "https://api.example.com/v1/status"
)

for url in "${ENDPOINTS[@]}"; do
  HTTP_CODE=$(curl -o /dev/null -s -w "%{http_code}" --max-time 10 "$url")
  LATENCY=$(curl -o /dev/null -s -w "%{time_total}" --max-time 10 "$url")

  if [ "$HTTP_CODE" != "200" ]; then
    echo "ALERT: $url returned HTTP $HTTP_CODE"
    # Send alert via webhook, PagerDuty, etc.
  fi

  if (( $(echo "$LATENCY > 5.0" | bc -l) )); then
    echo "WARNING: $url latency ${LATENCY}s exceeds 5s threshold"
  fi
done
```

### Using Datadog Synthetics

```bash
# Create an API test via Datadog API
curl -X POST "https://api.datadoghq.com/api/v1/synthetics/tests/api" \
  -H "DD-API-KEY: YOUR_API_KEY" \
  -H "DD-APPLICATION-KEY: YOUR_APP_KEY" \
  -d '{
    "name": "API Health Check",
    "type": "api",
    "subtype": "http",
    "config": {
      "request": {
        "method": "GET",
        "url": "https://api.example.com/health",
        "timeout": 10
      },
      "assertions": [
        {"type": "statusCode", "operator": "is", "target": 200},
        {"type": "responseTime", "operator": "lessThan", "target": 2000}
      ]
    },
    "locations": ["aws:us-east-1", "aws:eu-west-1", "aws:ap-southeast-1"],
    "options": {"tick_every": 120},
    "message": "API health check failed. Runbook: research/runbooks/slow-api-debugging.md @pagerduty"
  }'
```

## Step 3: Set up SSL certificate monitoring

```bash
#!/bin/bash
# Check SSL certificate expiration
DOMAIN="example.com"
EXPIRY=$(echo | openssl s_client -servername "$DOMAIN" -connect "$DOMAIN:443" 2>/dev/null | \
  openssl x509 -noout -enddate | cut -d= -f2)
EXPIRY_EPOCH=$(date -d "$EXPIRY" +%s)
NOW_EPOCH=$(date +%s)
DAYS_LEFT=$(( (EXPIRY_EPOCH - NOW_EPOCH) / 86400 ))

echo "SSL certificate for $DOMAIN expires in $DAYS_LEFT days"

if [ "$DAYS_LEFT" -lt 30 ]; then
  echo "WARNING: SSL certificate expires in $DAYS_LEFT days!"
fi

if [ "$DAYS_LEFT" -lt 7 ]; then
  echo "CRITICAL: SSL certificate expires in $DAYS_LEFT days!"
fi
```

## Step 4: Set up DNS monitoring

```bash
#!/bin/bash
# Check DNS resolution
DOMAIN="example.com"
EXPECTED_IP="93.184.216.34"

RESOLVED_IP=$(dig +short "$DOMAIN" @8.8.8.8 | head -1)

if [ "$RESOLVED_IP" != "$EXPECTED_IP" ]; then
  echo "ALERT: DNS for $DOMAIN resolved to $RESOLVED_IP, expected $EXPECTED_IP"
fi
```

## Monitoring From Multiple Locations

Run checks from at least 3 geographic regions. If only one location fails, it might be a network issue, not a service issue.

**Alert logic:** Alert only when 2+ locations report failure simultaneously.

## Related

- `research/monitoring/slo-sli-guide.md` — Synthetic checks feed SLI measurement
- `research/monitoring/alert-fatigue.md` — Alert tuning for synthetic checks
- `research/runbooks/dns-outage.md` — DNS failure response
- `entities/datadog.md` — Datadog Synthetics setup
