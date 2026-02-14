---
title: "Credential Rotation Runbook"
tags: [credentials, rotation, security, secrets, API-keys, runbook]
content_type: research
domain: operations
---

# Credential Rotation

## The Question

What is the procedure for rotating compromised credentials?

## When To Use

- Credentials exposed in a git commit, log file, or error message
- Employee with access departs (voluntary or involuntary)
- Security incident confirmed or suspected
- Regular rotation schedule (every 90 days recommended)

## Severity

**P0** if credentials are actively compromised. **P2** if rotation is precautionary.

## Step 1: Identify all affected credentials (5 minutes)

Make a list. Common credential types:

- [ ] AWS IAM access keys
- [ ] Database passwords
- [ ] API keys (third-party services)
- [ ] SSH keys
- [ ] TLS/SSL certificates
- [ ] OAuth client secrets
- [ ] Service account tokens (Kubernetes)
- [ ] CI/CD pipeline secrets

## Step 2: Rotate AWS IAM credentials

```bash
# List all access keys for a user
aws iam list-access-keys --user-name SERVICE_USER

# Create new access key
aws iam create-access-key --user-name SERVICE_USER

# Update the new key in your secrets manager
# (varies by tool — AWS Secrets Manager example)
aws secretsmanager update-secret --secret-id prod/api/aws-key \
  --secret-string '{"access_key":"AKIANEWKEY","secret_key":"newsecretvalue"}'

# Deactivate old key (do NOT delete yet — verify new key works first)
aws iam update-access-key --access-key-id AKIAOLDKEY \
  --status Inactive --user-name SERVICE_USER

# After verification, delete old key
aws iam delete-access-key --access-key-id AKIAOLDKEY --user-name SERVICE_USER
```

## Step 3: Rotate database passwords

```bash
# PostgreSQL
psql -h db.example.com -U admin -c "ALTER USER app_user WITH PASSWORD 'new_secure_password';"

# MySQL
mysql -h db.example.com -u admin -p -e "ALTER USER 'app_user'@'%' IDENTIFIED BY 'new_secure_password'; FLUSH PRIVILEGES;"

# Update in secrets manager
aws secretsmanager update-secret --secret-id prod/db/password \
  --secret-string '{"password":"new_secure_password"}'

# Restart application to pick up new credentials
kubectl rollout restart deployment/api-server -n production
```

## Step 4: Rotate API keys

```bash
# For each third-party service:
# 1. Generate new key in the service's dashboard
# 2. Update in secrets manager
aws secretsmanager update-secret --secret-id prod/stripe/api-key \
  --secret-string '{"key":"sk_live_newkeyvalue"}'

# 3. Deploy application to use new key
kubectl rollout restart deployment/payment-service -n production

# 4. Revoke old key in the service's dashboard
# 5. Verify integration still works
curl -s https://your-service.example.com/health/integrations
```

## Step 5: Rotate Kubernetes secrets

```bash
# Delete and recreate secret
kubectl delete secret db-credentials -n production
kubectl create secret generic db-credentials -n production \
  --from-literal=username=app_user \
  --from-literal=password='new_secure_password'

# Restart pods that use this secret
kubectl rollout restart deployment/api-server -n production

# Verify pods are running with new secret
kubectl get pods -n production -l app=api-server
```

## Step 6: Rotate SSH keys

```bash
# Generate new key pair
ssh-keygen -t ed25519 -C "deploy@example.com" -f ~/.ssh/deploy_key_new

# Add new public key to authorized hosts
# Then remove old public key from authorized_keys on all servers

# Update CI/CD pipeline with new private key
# (GitHub Actions, GitLab CI, etc.)

# Test connectivity
ssh -i ~/.ssh/deploy_key_new deploy@server.example.com "echo connected"
```

## Step 7: Verify everything works

```bash
# Run health checks against all services
for svc in api-server payment-service auth-service; do
  echo "Checking $svc..."
  kubectl exec -n production deploy/$svc -- curl -s localhost:8080/health
done

# Check for authentication errors in logs
kubectl logs -n production -l app=api-server --since=5m | grep -i "auth\|credential\|denied"
```

## Post-Rotation

1. Document which credentials were rotated and when
2. Update rotation schedule in your password manager
3. Scan git history for the compromised credential
4. Add pre-commit hooks to prevent future credential leaks (e.g., git-secrets, trufflehog)

## Related

- `research/incidents/security-incident-response.md` — Full security incident procedure
- `research/incidents/incident-classification.md` — Severity classification
