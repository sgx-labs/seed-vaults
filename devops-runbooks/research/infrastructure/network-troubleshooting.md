---
title: "Network Troubleshooting Runbook"
tags: [network, connectivity, DNS, firewall, latency, troubleshooting, runbook, security-groups, TLS]
content_type: research
domain: operations
---

# Network Troubleshooting

## The Question

How do I diagnose and fix network connectivity issues in production?

## Step 1: Identify the layer

```
OSI Model (simplified for ops):
Layer 3 (Network):    Can you ping the host?
Layer 4 (Transport):  Can you connect to the port?
Layer 7 (Application): Does the service respond correctly?
```

## Step 2: Layer-by-layer diagnosis

### Layer 3: Network reachability

```bash
# Can you reach the host?
ping -c 3 target.example.com

# Trace the route
traceroute target.example.com
# or
mtr target.example.com  # Better: continuous traceroute

# Check if it is a DNS issue (try by IP)
ping -c 3 10.0.1.50
```

### Layer 4: Port connectivity

```bash
# Is the port open and accepting connections?
nc -zv target.example.com 443 -w 5
# Expected: "Connection to target.example.com port 443 [tcp/https] succeeded!"

# Check from within Kubernetes
kubectl run test-net --rm -it --image=nicolaka/netshoot -- \
  bash -c "nc -zv service-name.namespace.svc.cluster.local 8080 -w 5"

# Check which ports are listening on the target
ss -tlnp  # TCP listening ports
ss -ulnp  # UDP listening ports
```

### Layer 7: Application response

```bash
# Does the service respond correctly?
curl -v https://target.example.com/health

# Check with timing
curl -o /dev/null -s -w "DNS: %{time_namelookup}s\nConnect: %{time_connect}s\nTLS: %{time_appconnect}s\nTTFB: %{time_starttransfer}s\nTotal: %{time_total}s\n" \
  https://target.example.com/health

# If high DNS time: DNS issue → see research/runbooks/dns-outage.md
# If high connect time: Network/firewall issue
# If high TLS time: Certificate or TLS handshake issue
# If high TTFB: Application is slow → see research/runbooks/slow-api-debugging.md
```

## Common Issues and Fixes

### Security group / firewall blocking

```bash
# AWS: Check security group rules
aws ec2 describe-security-groups --group-ids sg-12345678 \
  --query 'SecurityGroups[0].IpPermissions'

# Add inbound rule
aws ec2 authorize-security-group-ingress --group-id sg-12345678 \
  --protocol tcp --port 443 --cidr 10.0.0.0/16

# Kubernetes: Check NetworkPolicy
kubectl get networkpolicy -n production
kubectl describe networkpolicy POLICY_NAME -n production
```

### Kubernetes service DNS not resolving

```bash
# Check CoreDNS is running
kubectl get pods -n kube-system -l k8s-app=kube-dns

# Test DNS resolution from a pod
kubectl run test-dns --rm -it --image=busybox -- \
  nslookup service-name.namespace.svc.cluster.local

# Check if the service exists and has endpoints
kubectl get svc SERVICE -n production
kubectl get endpoints SERVICE -n production
```

### SSL/TLS certificate issues

```bash
# Check certificate validity
echo | openssl s_client -servername example.com -connect example.com:443 2>/dev/null | \
  openssl x509 -noout -dates

# Check certificate chain
echo | openssl s_client -servername example.com -connect example.com:443 -showcerts 2>/dev/null

# Common issues:
# - Certificate expired → renew immediately
# - Certificate name mismatch → check CN/SAN
# - Incomplete chain → add intermediate certificates
```

### High latency between services

```bash
# Measure latency within the cluster
kubectl run test-latency --rm -it --image=nicolaka/netshoot -- \
  bash -c "hping3 -S -p 8080 -c 10 service-name.namespace.svc.cluster.local"

# Check for packet loss
kubectl run test-loss --rm -it --image=nicolaka/netshoot -- \
  bash -c "ping -c 100 service-name.namespace.svc.cluster.local | tail -5"
```

## Related

- `research/runbooks/dns-outage.md` — DNS-specific troubleshooting
- `research/runbooks/slow-api-debugging.md` — Slow application response
- `research/infrastructure/kubernetes-failure-modes.md` — K8s networking
- `hub/infrastructure.md` — Infrastructure overview
