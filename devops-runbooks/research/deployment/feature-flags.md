---
title: "Feature Flag Management"
tags: [feature-flags, rollback, gradual-rollout, kill-switch, deployment]
content_type: research
domain: operations
---

# Feature Flag Management

## The Question

How do I use feature flags for safer deployments and instant rollback?

## Core Concept

Feature flags decouple deployment from release. You deploy code to production but control who sees it with a flag. If something breaks, you flip the flag off — no redeployment needed.

## Flag Types

| Type | Lifetime | Example |
|------|----------|---------|
| **Release flag** | Days-weeks | New checkout flow (gradual rollout) |
| **Ops flag** | Permanent | Enable debug logging, maintenance mode |
| **Kill switch** | Permanent | Disable payment processing in emergency |
| **Experiment** | Days-weeks | A/B test: button color |
| **Permission** | Permanent | Beta features for premium users |

## Implementation Pattern

```python
# Pseudocode — works with any flag provider
if feature_flags.is_enabled("new-checkout-flow", user=current_user):
    return render_new_checkout()
else:
    return render_old_checkout()
```

## Gradual Rollout Strategy

```
Day 1:  0% → 1% of users (canary)
        Monitor for 24 hours. Check error rates.

Day 2:  1% → 10%
        Monitor for 24 hours.

Day 3:  10% → 25%
        Monitor for 24 hours.

Day 4:  25% → 50%
        Monitor for 24 hours.

Day 5:  50% → 100%
        Monitor for 48 hours, then clean up flag.
```

## Emergency: Disable a flag

```bash
# LaunchDarkly CLI
ldcli flags update --project default --flag new-checkout-flow \
  --env production --off

# Environment variable approach
kubectl set env deployment/api-server -n production \
  FEATURE_NEW_CHECKOUT=false
kubectl rollout restart deployment/api-server -n production

# Config file approach
# Update config, then restart
kubectl edit configmap feature-flags -n production
kubectl rollout restart deployment/api-server -n production
```

## Flag Hygiene

| Rule | Why |
|------|-----|
| Clean up flags after full rollout | Dead flags are tech debt |
| Every flag has an owner | No orphaned flags |
| Set expiration dates on release flags | Force cleanup |
| Test both flag states | The "off" path must still work |
| Log flag evaluations | Know which users saw which variant |

## Kill Switches (Always Keep These)

```
maintenance_mode:    true/false — Shows maintenance page
disable_payments:    true/false — Stops all payment processing
read_only_mode:      true/false — Disables all write operations
rate_limit_override: true/false — Emergency rate limiting
```

These flags should be permanent, well-tested, and instantly accessible during incidents.

## Related

- `research/deployment/rollback-procedures.md` — Rollback strategies
- `research/deployment/failed-deployment.md` — Failed deployment response
- `research/deployment/deployment-strategies.md` — Canary and blue-green
- `hub/deployment.md` — Deployment overview
