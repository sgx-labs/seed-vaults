---
title: "CI/CD Pipeline Security"
tags: [infrastructure, ci-cd, github-actions, pipeline, supply-chain, oidc, sha-pinning]
content_type: research
domain: security
---

# CI/CD Pipeline Security

Your CI/CD pipeline has access to production secrets, deployment credentials, and source code. If compromised, attackers have everything.

## Threat Model

| Threat | Impact | Mitigation |
|--------|--------|------------|
| Compromised dependency in build | Code injection | Pin dependencies, use lockfiles |
| Stolen CI secrets | Credential theft | Limit secret scope, use OIDC |
| Malicious PR from fork | Code execution | Require approval for fork PRs |
| Unpinned actions/orbs | Supply chain attack | Pin to commit SHA |
| Over-privileged tokens | Lateral movement | Least-privilege GITHUB_TOKEN |

## GitHub Actions Hardening

### Restrict GITHUB_TOKEN Permissions

```yaml
# SECURE — Minimal token permissions at workflow level
name: CI
permissions:
  contents: read
  pull-requests: read

on: [push, pull_request]

jobs:
  build:
    runs-on: ubuntu-latest
    # Job-level permissions override workflow-level
    permissions:
      contents: read
    steps:
      - uses: actions/checkout@b4ffde65f46336ab88eb53be808477a3936bae11
```

### Pin Actions to SHA

```yaml
# SECURE — Always pin to full commit SHA
- uses: actions/checkout@b4ffde65f46336ab88eb53be808477a3936bae11 # v4.1.1
- uses: actions/setup-node@60edb5dd545a775178f52524783378180af0d1f8 # v4.0.2

# VULNERABLE — Tags can be force-pushed
# - uses: actions/checkout@v4    # DO NOT USE
# - uses: some-action@main       # DO NOT USE
```

### Use OIDC Instead of Long-Lived Secrets

```yaml
# SECURE — AWS OIDC authentication (no stored credentials)
permissions:
  id-token: write
  contents: read

steps:
  - uses: aws-actions/configure-aws-credentials@e3dd6a429d7300a6a4c196c26e071d42e0343502
    with:
      role-to-assume: arn:aws:iam::ACCOUNT_ID:role/github-actions-deploy
      aws-region: us-east-1
      # No access keys needed - uses OIDC federation
```

### Fork PR Protection

```yaml
# SECURE — Require approval for fork PRs
# In repository Settings > Actions > General:
# "Require approval for all outside collaborators"

# In workflow:
on:
  pull_request_target:  # Runs in context of base, not fork
    types: [labeled]    # Only when labeled (manual gate)
```

## Secret Management in CI

```yaml
# SECURE — Environment-scoped secrets
jobs:
  deploy:
    environment: production  # Requires approval + environment secrets
    steps:
      - name: Deploy
        env:
          # Secrets scoped to 'production' environment only
          DB_URL: ${{ secrets.PRODUCTION_DB_URL }}
```

**Best practices:**
- Use environment-level secrets (not repository-level) for production
- Require approval for production environment deployments
- Never echo or print secrets (even masked, they can leak)
- Use OIDC for cloud provider authentication
- Rotate CI secrets regularly

## Secure Build Practices

1. **Reproducible builds** — Same input always produces same output
2. **Isolated build environments** — Fresh runner for each build
3. **Verify artifacts** — Sign build outputs, verify before deployment
4. **Audit trail** — Log who triggered what deployment and when
5. **Rollback capability** — Can revert to previous version in minutes

## How to Test

1. Review all GitHub Actions for unpinned actions
2. Check GITHUB_TOKEN permissions (should not be `write-all`)
3. Verify fork PRs require approval before running workflows
4. Check that production secrets are environment-scoped
5. Run StepSecurity Harden-Runner for action analysis

## See Also

- hub/infrastructure.md
- research/dependencies/ci-cd-scanning.md
- research/owasp/supply-chain-failures.md
