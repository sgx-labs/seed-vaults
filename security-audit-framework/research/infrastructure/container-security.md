---
title: "Container Security Best Practices"
tags: [infrastructure, containers, docker, kubernetes, scanning, distroless, non-root, trivy]
content_type: research
domain: security
---

# Container Security Best Practices

Containers are not security boundaries by default. Without hardening, they inherit the host's attack surface.

## Dockerfile Security

### Vulnerable: Common Mistakes — DO NOT USE

```dockerfile
# VULNERABLE — Multiple security issues
FROM ubuntu:latest
RUN apt-get update && apt-get install -y curl nodejs
COPY . /app
WORKDIR /app
RUN npm install
EXPOSE 3000
# Runs as root (default)
CMD ["node", "server.js"]
```

### Secure: Hardened Dockerfile

```dockerfile
# SECURE — Multi-stage, non-root, minimal image
FROM node:20-slim AS build
WORKDIR /app
COPY package.json package-lock.json ./
RUN npm ci --production
COPY src/ src/

FROM gcr.io/distroless/nodejs20-debian12
WORKDIR /app
COPY --from=build /app /app

# Non-root user (distroless uses nonroot by default)
USER nonroot

EXPOSE 3000
CMD ["src/server.js"]
```

## Key Principles

| Practice | Why |
|----------|-----|
| Use specific tags, not :latest | Reproducible builds |
| Use multi-stage builds | Smaller image, no build tools in prod |
| Run as non-root | Limit container escape impact |
| Use distroless/alpine base | Minimal attack surface |
| Copy only needed files | No source code, tests, docs in prod image |
| Use .dockerignore | Prevent secrets from entering image |

## .dockerignore

```
.git
.env
.env.*
node_modules
*.md
tests/
.github/
*.pem
*.key
```

## Container Scanning

```bash
# Trivy — Scan container image
trivy image myapp:latest --severity HIGH,CRITICAL

# Grype — Alternative scanner
grype myapp:latest

# Docker Scout (Docker Desktop)
docker scout cves myapp:latest
```

## Kubernetes Security

```yaml
# SECURE — Pod security context
apiVersion: v1
kind: Pod
metadata:
  name: app
spec:
  securityContext:
    runAsNonRoot: true
    runAsUser: 65534  # nobody
    fsGroup: 65534
    seccompProfile:
      type: RuntimeDefault
  containers:
    - name: app
      image: myapp@sha256:abc123...  # Pin by digest
      securityContext:
        allowPrivilegeEscalation: false
        readOnlyRootFilesystem: true
        capabilities:
          drop: ["ALL"]
      resources:
        limits:
          memory: "256Mi"
          cpu: "500m"
      volumeMounts:
        - name: tmp
          mountPath: /tmp
  volumes:
    - name: tmp
      emptyDir: {}
  automountServiceAccountToken: false  # Unless needed
```

## Common Mistakes

- Running as root (default in most base images)
- Using `:latest` tag (unpredictable, non-reproducible)
- Mounting Docker socket inside containers (container escape)
- Not scanning images for CVEs before deployment
- Storing secrets in image layers or environment variables
- Using privileged mode or host networking

## How to Test

1. Run `trivy image` on your production images
2. Check Dockerfile for non-root user configuration
3. Verify `.dockerignore` excludes sensitive files
4. Check Kubernetes pod specs for security context
5. Verify images are pinned by digest in production

## See Also

- hub/infrastructure.md
- research/infrastructure/cloud-security.md
- entities/security-tools.md
