---
title: "DNS Security and Subdomain Takeover"
tags: [infrastructure, dns, subdomain-takeover, dnssec, caa-records, cname, domain-security]
content_type: research
domain: security
---

# DNS Security and Subdomain Takeover

DNS misconfigurations can redirect your users to attacker-controlled servers. Subdomain takeover is one of the most common and impactful DNS vulnerabilities.

## Subdomain Takeover

### How It Happens

1. Your company creates `blog.example.com` pointing to a cloud service (e.g., GitHub Pages, Heroku, S3)
2. You stop using the cloud service but forget to remove the DNS record
3. Attacker claims the resource on the cloud service
4. `blog.example.com` now serves attacker-controlled content

### Vulnerable Services

| Service | CNAME Pattern | Takeover Risk |
|---------|--------------|---------------|
| GitHub Pages | *.github.io | High |
| Heroku | *.herokuapp.com | High |
| AWS S3 | *.s3.amazonaws.com | High |
| Azure | *.azurewebsites.net | Medium |
| Shopify | *.myshopify.com | Medium |
| Netlify | *.netlify.app | Medium |

### Prevention

```bash
# Audit DNS records regularly
# Check for dangling CNAMEs that point to unclaimed resources
dig CNAME blog.example.com
# If the target does not resolve or returns NXDOMAIN, it may be vulnerable

# Remove unused DNS records as part of decommissioning procedures
```

**Checklist:**
- [ ] Audit DNS records quarterly
- [ ] Remove DNS records when decommissioning services
- [ ] Monitor for new subdomains (certificate transparency logs)
- [ ] Use cloud provider resource locking where available
- [ ] Include DNS cleanup in offboarding/decommission runbooks

## DNSSEC

DNSSEC (DNS Security Extensions) adds cryptographic signatures to DNS records, preventing DNS spoofing and cache poisoning.

**Enable DNSSEC when:**
- Your registrar and DNS provider support it
- You handle sensitive data (finance, healthcare)
- You want to prevent DNS cache poisoning attacks

## CAA Records

Certificate Authority Authorization records specify which CAs can issue certificates for your domain:

```
example.com.  IN  CAA  0 issue "letsencrypt.org"
example.com.  IN  CAA  0 issue "digicert.com"
example.com.  IN  CAA  0 iodef "mailto:security@example.com"
```

This prevents unauthorized certificate issuance.

## How to Test

1. Enumerate subdomains (certificate transparency logs, DNS brute-force)
2. Check each subdomain's CNAME target for unclaimed resources
3. Verify DNSSEC is configured (dnsviz.net)
4. Check for CAA records (dig CAA example.com)
5. Use tools like subjack, nuclei, or can-i-take-over-xyz

## See Also

- hub/infrastructure.md
- research/owasp/security-misconfiguration.md
- research/infrastructure/cloud-security.md
