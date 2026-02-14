---
title: "SSL/TLS Certificate Issues Runbook"
tags: [SSL, TLS, certificate, expired, HTTPS, runbook, P0]
content_type: research
domain: operations
---

# SSL/TLS Certificate Issues Runbook

## The Question

What do I do when SSL/TLS certificates expire or cause errors?

## Symptoms

- Browser showing "Your connection is not private" or "NET::ERR_CERT_DATE_INVALID"
- API clients failing with "certificate verify failed" or "SSL handshake error"
- Health checks failing on HTTPS endpoints
- `curl: (60) SSL certificate problem: certificate has expired`

## Severity: P0 (if affecting production HTTPS endpoints)

## Step 1: Diagnose the certificate issue (2 minutes)

```bash
# Check certificate expiration
echo | openssl s_client -servername example.com -connect example.com:443 2>/dev/null | \
  openssl x509 -noout -dates -subject -issuer

# Check how many days until expiration
echo | openssl s_client -servername example.com -connect example.com:443 2>/dev/null | \
  openssl x509 -noout -enddate | \
  awk -F= '{print $2}' | \
  xargs -I{} sh -c 'echo $(( ( $(date -d "{}" +%s) - $(date +%s) ) / 86400 )) days remaining'

# Check the full certificate chain
echo | openssl s_client -servername example.com -connect example.com:443 -showcerts 2>/dev/null

# Check for certificate mismatch (wrong domain)
echo | openssl s_client -servername example.com -connect example.com:443 2>/dev/null | \
  openssl x509 -noout -text | grep -A1 "Subject Alternative Name"
```

## Step 2: Fix based on cause

### Cause: Certificate expired

```bash
# If using Let's Encrypt / Certbot
sudo certbot renew --force-renewal
sudo systemctl reload nginx  # or apache

# If using AWS Certificate Manager (ACM)
# ACM auto-renews, but check status:
aws acm describe-certificate --certificate-arn CERT_ARN \
  --query 'Certificate.[Status,NotAfter,RenewalSummary]'

# If ACM renewal failed (usually DNS validation issue):
aws acm describe-certificate --certificate-arn CERT_ARN \
  --query 'Certificate.DomainValidationOptions'
# Verify the CNAME record exists in DNS

# Kubernetes: cert-manager
kubectl get certificate -n production
kubectl describe certificate CERT_NAME -n production
# Force renewal:
kubectl delete secret CERT_SECRET_NAME -n production
# cert-manager will re-issue
```

### Cause: Certificate chain incomplete

```bash
# Download and install intermediate certificates
# Find your CA's intermediate cert on their website
# Concatenate in order: server cert + intermediate + root
cat server.crt intermediate.crt > fullchain.crt

# Nginx: update ssl_certificate to point to fullchain
# ssl_certificate /etc/ssl/fullchain.crt;

# Verify the chain is complete
openssl verify -CAfile /etc/ssl/certs/ca-certificates.crt fullchain.crt
```

### Cause: Wrong certificate for the domain

```bash
# Check which domains the certificate covers
echo | openssl s_client -servername example.com -connect example.com:443 2>/dev/null | \
  openssl x509 -noout -text | grep "DNS:"

# If the domain is not listed, you need a new certificate
# Let's Encrypt:
sudo certbot certonly --nginx -d example.com -d www.example.com
```

## Step 3: Verify the fix

```bash
# Test HTTPS connection
curl -v https://example.com/ 2>&1 | grep -E "SSL|certificate|expire"

# Test from outside (different DNS cache)
curl -o /dev/null -s -w "HTTP %{http_code}\n" https://example.com/

# Check certificate details again
echo | openssl s_client -servername example.com -connect example.com:443 2>/dev/null | \
  openssl x509 -noout -dates
```

## Prevention

1. Set up certificate expiration monitoring (alert at 30, 14, 7 days)
2. Use auto-renewing certificates (Let's Encrypt, ACM, cert-manager)
3. Keep a list of all certificates and their expiration dates
4. Test certificate renewal process in staging

## Related

- `research/monitoring/synthetic-monitoring.md` — TLS monitoring setup
- `research/infrastructure/network-troubleshooting.md` — Network connectivity
- `research/runbooks/dns-outage.md` — DNS-related certificate issues
