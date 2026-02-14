---
title: "Secrets Management — From Dev to Production"
tags: [infrastructure, secrets, vault, kms, environment-variables]
content_type: research
domain: security
---

# Secrets Management — From Dev to Production

Secrets (API keys, database credentials, encryption keys) are the keys to your kingdom. How you manage them determines your blast radius when something goes wrong.

## The Hierarchy (Best to Worst)

1. **Hardware Security Module (HSM)** — For signing keys, root credentials
2. **Secrets Manager** (Vault, AWS SM, GCP SM) — For application secrets
3. **Encrypted config files** — For local development only
4. **Environment variables** — Visible in process listings; avoid in production
5. **Hardcoded in source** — NEVER (committed to git = compromised forever)

## Why Not Environment Variables?

Environment variables are often recommended but have significant risks:

- Visible via `/proc/PID/environ` on Linux
- Logged in crash dumps and debugging output
- Inherited by child processes (including third-party code)
- Visible in CI/CD logs if not masked
- No access control, audit logging, or rotation

## HashiCorp Vault Integration

```javascript
// SECURE — Node.js with HashiCorp Vault
const vault = require('node-vault')({
  apiVersion: 'v1',
  endpoint: 'https://vault.internal.example.com:8200',
  token: process.env.VAULT_TOKEN, // Bootstrap token only
});

async function getSecret(path) {
  const result = await vault.read(`secret/data/${path}`);
  return result.data.data;
}

// Usage
const dbCredentials = await getSecret('production/database');
const pool = new Pool({
  host: dbCredentials.host,
  user: dbCredentials.username,
  password: dbCredentials.password,
  database: dbCredentials.name,
  ssl: { rejectUnauthorized: true },
});
```

## AWS Secrets Manager

```python
# SECURE — Python with AWS Secrets Manager
import json
import boto3

def get_secret(secret_name):
    client = boto3.client('secretsmanager', region_name='us-east-1')
    response = client.get_secret_value(SecretId=secret_name)
    return json.loads(response['SecretString'])

# Usage
db_creds = get_secret('production/database')
connection = psycopg2.connect(
    host=db_creds['host'],
    user=db_creds['username'],
    password=db_creds['password'],
    dbname=db_creds['name'],
    sslmode='require',
)
```

## Kubernetes Secrets (with encryption)

```yaml
# SECURE — External Secrets Operator pulls from cloud provider
apiVersion: external-secrets.io/v1beta1
kind: ExternalSecret
metadata:
  name: db-credentials
spec:
  refreshInterval: 1h
  secretStoreRef:
    name: aws-secrets-manager
    kind: ClusterSecretStore
  target:
    name: db-credentials
  data:
    - secretKey: username
      remoteRef:
        key: production/database
        property: username
    - secretKey: password
      remoteRef:
        key: production/database
        property: password
```

## Local Development

```bash
# .env file — NEVER commit to git
# Add to .gitignore FIRST
DATABASE_URL=postgresql://devuser:devpassword@localhost:5432/devdb
API_KEY=dev-only-test-key-not-for-production

# Use dotenv for local development only
# Production should use a secrets manager
```

```gitignore
# .gitignore — Critical entries
.env
.env.local
.env.production
*.pem
*.key
credentials.json
```

## Secret Rotation

1. Generate new secret in the secrets manager
2. Update application to use new secret (rolling deploy)
3. Verify new secret works in production
4. Revoke old secret
5. Audit that old secret is no longer in use

## How to Test

1. Run Gitleaks or TruffleHog on the repository history
2. Check that `.env` files are in `.gitignore`
3. Verify production does not use hardcoded secrets
4. Check that secrets are not logged (search logs for known patterns)
5. Verify secret rotation has been tested

## See Also

- hub/infrastructure.md
- research/owasp/cryptographic-failures.md
- research/infrastructure/sensitive-data-logs.md
