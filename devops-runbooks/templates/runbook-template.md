---
title: "Template — Runbook"
tags: [template, runbook, operations]
content_type: template
domain: operations
---

# Runbook Template

*Copy this template when creating a new runbook. Every step must have a concrete command or action.*

---

## Runbook: [Name of Procedure]

**Last tested:** YYYY-MM-DD
**Estimated time:** X minutes
**Required access:** [List permissions/roles needed]
**Severity if untreated:** P0 / P1 / P2

## Symptoms

- [Observable symptom 1 — what you see in monitoring or user reports]
- [Observable symptom 2]
- [Observable symptom 3]

## Prerequisites

- [ ] Access to [system/tool]
- [ ] VPN connected (if required)
- [ ] CLI tools installed: [list]

## Step 1: Assess the situation

```bash
# [Command to check current state]
```

**Expected output:** [What you should see]
**If unexpected:** [What to do instead]

## Step 2: [Diagnostic action]

```bash
# [Command to gather more information]
```

**Expected output:** [What you should see]

## Step 3: [Fix action]

```bash
# [Command to apply the fix]
```

**Verify the fix worked:**

```bash
# [Command to confirm resolution]
```

## Step 4: [Post-fix verification]

```bash
# [Command to verify service is fully healthy]
```

## Rollback

If the fix makes things worse:

```bash
# [Commands to undo the fix]
```

## Escalation

If this runbook does not resolve the issue:

1. Escalate to [role/team]
2. Provide: [what information to include]
3. Reference: [related runbook or documentation]

## Related Runbooks

- `[path to related runbook 1]`
- `[path to related runbook 2]`
