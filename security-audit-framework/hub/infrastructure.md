---
title: "Infrastructure & Cloud Security"
tags: [infrastructure, cloud, secrets, network, containers]
content_type: hub
domain: security
---

# Infrastructure & Cloud Security

Secure code on insecure infrastructure is still insecure. This covers secrets management, cloud configuration, and operational security.

## Core Principles

1. **Secrets never in code** — Use a secrets manager (Vault, AWS Secrets Manager, etc.)
2. **Least privilege everywhere** — IAM roles, network rules, container permissions
3. **Encrypt in transit and at rest** — TLS 1.3 for transit, AES-256 for storage
4. **Immutable infrastructure** — Build new, do not patch in place
5. **Monitor everything** — You cannot secure what you cannot see

## Secrets Management Hierarchy

| Method | Security Level | Use Case |
|--------|---------------|----------|
| Hardware Security Module | Highest | Signing keys, root credentials |
| Secrets Manager (Vault, AWS SM) | High | Application secrets, API keys |
| Encrypted environment files | Medium | Local development only |
| Plain environment variables | LOW/AVOID | Visible in process listings |
| Hardcoded in source | NEVER | Committed to git = compromised |

See: research/infrastructure/secrets-management.md

## Container Security

- Use minimal base images (distroless, alpine)
- Run as non-root user
- Scan images for vulnerabilities (Trivy, Grype)
- Do not mount the Docker socket
- Use read-only root filesystem where possible

See: research/infrastructure/container-security.md

## Cloud Security Basics

- Enable MFA on all cloud accounts, especially root/admin
- Use IAM roles, not long-lived access keys
- Enable CloudTrail/audit logging on day one
- Review S3/GCS bucket ACLs — public buckets are breach #1
- Use VPCs and security groups to restrict network access

See: research/infrastructure/cloud-security.md

## Research Notes

| Topic | Note |
|-------|------|
| Secrets Management | research/infrastructure/secrets-management.md |
| Container Security | research/infrastructure/container-security.md |
| Cloud Security | research/infrastructure/cloud-security.md |
| Incident Response | research/infrastructure/incident-response.md |
| Sensitive Data in Logs | research/infrastructure/sensitive-data-logs.md |
| CI/CD Pipeline Security | research/infrastructure/cicd-security.md |

## See Also

- hub/dependencies.md — Build pipeline security
- hub/compliance.md — Infrastructure compliance requirements
- entities/security-tools.md — Infrastructure scanning tools
