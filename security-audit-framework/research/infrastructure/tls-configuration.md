---
title: "TLS Configuration Best Practices"
tags: [infrastructure, tls, ssl, https, encryption-in-transit, nginx, lets-encrypt, cipher-suites]
content_type: research
domain: security
---

# TLS Configuration Best Practices

TLS (Transport Layer Security) encrypts data in transit. Misconfigured TLS can be worse than no TLS — it creates a false sense of security.

## Current Standards

| Version | Status | Recommendation |
|---------|--------|----------------|
| TLS 1.3 | Current | Preferred — faster, more secure |
| TLS 1.2 | Acceptable | Minimum — configure strong cipher suites |
| TLS 1.1 | Deprecated | Disable |
| TLS 1.0 | Deprecated | Disable |
| SSL 3.0 | Broken | Disable |
| SSL 2.0 | Broken | Disable |

## Nginx Configuration

```nginx
# SECURE — Modern TLS configuration
server {
    listen 443 ssl http2;
    server_name example.com;

    ssl_certificate     /etc/ssl/certs/example.com.crt;
    ssl_certificate_key /etc/ssl/private/example.com.key;

    # Protocols
    ssl_protocols TLSv1.2 TLSv1.3;

    # Cipher suites (TLS 1.2 — TLS 1.3 manages its own)
    ssl_ciphers ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-RSA-AES256-GCM-SHA384;
    ssl_prefer_server_ciphers off;  # Let client choose (modern clients choose well)

    # OCSP Stapling
    ssl_stapling on;
    ssl_stapling_verify on;

    # HSTS
    add_header Strict-Transport-Security "max-age=63072000; includeSubDomains" always;

    # Redirect HTTP to HTTPS
    # (In a separate server block listening on port 80)
}
```

## Node.js HTTPS Configuration

```javascript
// SECURE — Node.js HTTPS server with modern TLS
const https = require('https');
const fs = require('fs');

const server = https.createServer({
  cert: fs.readFileSync('/etc/ssl/certs/example.com.crt'),
  key: fs.readFileSync('/etc/ssl/private/example.com.key'),
  minVersion: 'TLSv1.2',
  // TLS 1.3 ciphers are managed automatically
  ciphers: [
    'TLS_AES_128_GCM_SHA256',
    'TLS_AES_256_GCM_SHA384',
    'ECDHE-RSA-AES128-GCM-SHA256',
    'ECDHE-RSA-AES256-GCM-SHA384',
  ].join(':'),
}, app);
```

## Certificate Management

### Let's Encrypt (Recommended for Most)

```bash
# Certbot for automated certificate management
sudo certbot --nginx -d example.com -d www.example.com

# Auto-renewal (certbot creates a cron/systemd timer)
sudo certbot renew --dry-run
```

### Certificate Best Practices

1. Use certificates from trusted CAs (Let's Encrypt is free and trusted)
2. Set up automatic renewal (certificates expire after 90 days)
3. Use RSA 2048+ or ECDSA P-256 keys
4. Monitor certificate expiration with alerting
5. Set CAA DNS records to restrict authorized CAs

## Common Mistakes

- Supporting TLS 1.0/1.1 (deprecated, not PCI DSS compliant)
- Using self-signed certificates in production
- Not redirecting HTTP to HTTPS
- Missing HSTS header
- Certificate chain incomplete (missing intermediate certificates)
- Private key with wrong permissions (should be 600, owned by root)

## How to Test

1. Use SSL Labs (ssllabs.com/ssltest) for comprehensive analysis
2. Run `testssl.sh` for command-line testing
3. Verify HSTS header is present with appropriate max-age
4. Check certificate chain completeness
5. Verify HTTP redirects to HTTPS (no mixed content)

## See Also

- hub/infrastructure.md
- research/owasp/cryptographic-failures.md
- research/api-security/security-headers.md
