---
title: "Infrastructure & Cloud Security"
tags: [infrastructure, cloud-security, secrets-management, containers, tls, ci-cd, incident-response]
content_type: hub
domain: security
---

# Infrastructure & Cloud Security

Infrastructure and cloud security ensures your production environment protects the application it hosts. This hub covers secrets management with HashiCorp Vault and AWS Secrets Manager, container security with Docker hardening and Kubernetes pod policies, cloud IAM least-privilege policies for AWS, GCP, and Azure, TLS configuration with modern cipher suites, CI/CD pipeline hardening with pinned actions and OIDC authentication, DNS security and subdomain takeover prevention, incident response procedures, and sensitive data handling in logs. Every section links to implementation-ready research notes with code examples.

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

- hub/owasp-top-10.md — A02: Security Misconfiguration
- hub/authentication.md — Auth infrastructure, session stores
- hub/dependencies.md — Build pipeline security, container scanning
- hub/compliance.md — Infrastructure compliance requirements (SOC 2, HIPAA)
- entities/security-tools.md — Infrastructure scanning tools
- research/infrastructure/dns-security.md — DNS and subdomain takeover
- research/infrastructure/tls-configuration.md — TLS setup and cipher suites
