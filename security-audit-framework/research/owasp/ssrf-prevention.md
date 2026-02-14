---
title: "SSRF Prevention Patterns"
tags: [owasp, ssrf, server-side-request-forgery, network]
content_type: research
domain: security
---

# SSRF Prevention Patterns

Server-Side Request Forgery (SSRF) tricks your server into making requests to unintended destinations, including internal services, cloud metadata endpoints, and localhost. Rolled into A01:2025 (Broken Access Control).

## How SSRF Works

1. Application accepts a URL from user input (e.g., "fetch this image URL")
2. Server makes an HTTP request to that URL
3. Attacker provides an internal URL: `http://169.254.169.254/latest/meta-data/` (AWS metadata)
4. Server returns sensitive internal data to the attacker

## Common Attack Targets

| Target | URL | Risk |
|--------|-----|------|
| AWS metadata | http://169.254.169.254/ | IAM credentials, instance info |
| GCP metadata | http://metadata.google.internal/ | Service account tokens |
| Internal services | http://localhost:8080/admin | Admin access bypass |
| Internal network | http://10.0.0.1/api | Network scanning |
| Cloud functions | http://127.0.0.1:9001/ | Lambda runtime API |

## Vulnerable: URL Fetch Without Validation — DO NOT USE

```javascript
// VULNERABLE — Fetches any URL the user provides
app.post('/fetch-image', async (req, res) => {
  const response = await fetch(req.body.url);
  const image = await response.buffer();
  res.send(image);
});
```

## Secure: URL Validation

```javascript
// SECURE — Validate URL before fetching
const { URL } = require('url');
const dns = require('dns').promises;

const BLOCKED_HOSTS = [
  '169.254.169.254',      // AWS metadata
  'metadata.google.internal', // GCP metadata
  '100.100.100.200',      // Azure metadata
];

async function validateUrl(urlString) {
  let parsed;
  try {
    parsed = new URL(urlString);
  } catch {
    throw new Error('Invalid URL');
  }

  // Only allow HTTP(S)
  if (!['http:', 'https:'].includes(parsed.protocol)) {
    throw new Error('Invalid protocol');
  }

  // Block known metadata endpoints
  if (BLOCKED_HOSTS.includes(parsed.hostname)) {
    throw new Error('Blocked host');
  }

  // Resolve DNS and check for internal IPs
  const addresses = await dns.resolve4(parsed.hostname);
  for (const addr of addresses) {
    if (isInternalIP(addr)) {
      throw new Error('Internal IP not allowed');
    }
  }

  return parsed.toString();
}

function isInternalIP(ip) {
  const parts = ip.split('.').map(Number);
  return (
    parts[0] === 10 ||                              // 10.0.0.0/8
    (parts[0] === 172 && parts[1] >= 16 && parts[1] <= 31) || // 172.16.0.0/12
    (parts[0] === 192 && parts[1] === 168) ||        // 192.168.0.0/16
    (parts[0] === 127) ||                            // 127.0.0.0/8
    (parts[0] === 169 && parts[1] === 254) ||        // link-local
    parts[0] === 0                                    // 0.0.0.0/8
  );
}

app.post('/fetch-image', async (req, res) => {
  try {
    const validatedUrl = await validateUrl(req.body.url);
    const response = await fetch(validatedUrl, {
      redirect: 'error', // Do not follow redirects (redirect to internal)
    });
    const image = await response.buffer();
    res.send(image);
  } catch (err) {
    res.status(400).json({ error: 'Invalid URL' });
  }
});
```

## Cloud Metadata Protection

```bash
# AWS — Require IMDSv2 (token-based, blocks simple SSRF)
aws ec2 modify-instance-metadata-options \
  --instance-id i-1234567890abcdef0 \
  --http-tokens required \
  --http-endpoint enabled
```

## Defense Layers

1. **Input validation** — Parse and validate URLs before use
2. **DNS resolution check** — Resolve hostname and block internal IPs
3. **Network controls** — Firewall rules preventing outbound to internal ranges
4. **IMDSv2** — Require token for cloud metadata access
5. **Redirect blocking** — Do not follow redirects (can bypass hostname checks)
6. **Allowlist** — If possible, restrict to known-good domains

## How to Test

1. Try fetching `http://169.254.169.254/latest/meta-data/`
2. Try fetching `http://localhost:8080/`
3. Try fetching `http://10.0.0.1/`
4. Try a redirect: external URL that redirects to internal IP
5. Try DNS rebinding: hostname that resolves to internal IP

## See Also

- hub/owasp-top-10.md — A01: Broken Access Control
- research/owasp/broken-access-control.md
- research/infrastructure/cloud-security.md
