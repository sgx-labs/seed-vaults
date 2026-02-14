---
title: "Cloud Security Fundamentals"
tags: [infrastructure, cloud, aws, iam, s3, network, least-privilege, cloudtrail]
content_type: research
domain: security
---

# Cloud Security Fundamentals

Cloud misconfiguration is the #1 cause of cloud breaches. Default settings are almost never secure enough.

## Day-One Checklist

- [ ] Enable MFA on root/admin account
- [ ] Create individual IAM users (never share root credentials)
- [ ] Enable CloudTrail/audit logging
- [ ] Enable GuardDuty/Security Center (threat detection)
- [ ] Review default VPC and security group rules
- [ ] Enable billing alerts
- [ ] Set up a security contact email

## IAM Best Practices

### Vulnerable: Overly Broad Policy — DO NOT USE

```json
{
  "Effect": "Allow",
  "Action": "*",
  "Resource": "*"
}
```

### Secure: Least-Privilege Policy

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "s3:GetObject",
        "s3:PutObject"
      ],
      "Resource": "arn:aws:s3:::app-uploads-bucket/*",
      "Condition": {
        "StringEquals": {
          "aws:PrincipalTag/Environment": "production"
        }
      }
    }
  ]
}
```

### IAM Rules

1. **No long-lived access keys** — Use IAM roles and temporary credentials
2. **Separate environments** — Different accounts for dev/staging/prod
3. **Use conditions** — Restrict by IP, time, MFA status
4. **Regular review** — Audit unused permissions quarterly
5. **Service accounts** — Dedicated roles for each service, not shared credentials

## S3 / Object Storage Security

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "DenyUnencryptedObjectUploads",
      "Effect": "Deny",
      "Principal": "*",
      "Action": "s3:PutObject",
      "Resource": "arn:aws:s3:::my-bucket/*",
      "Condition": {
        "StringNotEquals": {
          "s3:x-amz-server-side-encryption": "aws:kms"
        }
      }
    },
    {
      "Sid": "DenyInsecureTransport",
      "Effect": "Deny",
      "Principal": "*",
      "Action": "s3:*",
      "Resource": [
        "arn:aws:s3:::my-bucket",
        "arn:aws:s3:::my-bucket/*"
      ],
      "Condition": {
        "Bool": { "aws:SecureTransport": "false" }
      }
    }
  ]
}
```

## Network Security

- Use VPCs with private subnets for databases and internal services
- Only expose load balancers and bastion hosts to the internet
- Use security groups as allowlists (deny all, allow specific)
- Enable VPC Flow Logs for network monitoring
- Use PrivateLink for AWS service access (avoid internet routing)

## Infrastructure as Code Security

```bash
# Scan Terraform for misconfigurations
checkov -d /path/to/terraform/
tfsec /path/to/terraform/

# Scan CloudFormation
cfn-lint template.yaml
checkov -f template.yaml
```

## How to Test

1. Run ScoutSuite or Prowler for cloud account audit
2. Check for public S3 buckets or storage accounts
3. Review IAM policies for `*` actions or resources
4. Verify CloudTrail/audit logging is enabled
5. Check security groups for 0.0.0.0/0 inbound rules

## See Also

- hub/infrastructure.md
- research/infrastructure/secrets-management.md
- research/owasp/security-misconfiguration.md
