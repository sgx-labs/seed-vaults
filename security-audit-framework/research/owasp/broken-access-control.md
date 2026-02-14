---
title: "A01:2025 — Broken Access Control"
tags: [owasp, access-control, authorization, idor, privilege-escalation]
content_type: research
domain: security
---

# A01:2025 — Broken Access Control

The #1 web application security risk. 3.73% of applications tested had access control flaws. Access control enforces policy so users cannot act outside their intended permissions.

## Common Vulnerabilities

- **IDOR** (Insecure Direct Object Reference) — Accessing other users' data by changing IDs
- **Privilege escalation** — Normal user accessing admin functions
- **Missing function-level checks** — Relying on UI hiding instead of server enforcement
- **CORS misconfiguration** — Allowing unauthorized origins to access APIs
- **Path traversal** — Accessing files outside intended directories

## Vulnerable: IDOR in Express.js

```javascript
// VULNERABLE — No ownership check
app.get('/api/invoices/:id', async (req, res) => {
  const invoice = await db.getInvoice(req.params.id);
  res.json(invoice); // Any user can access any invoice
});
```

**Why it is vulnerable:** The endpoint returns any invoice by ID without checking if the requesting user owns it.

## Secure: IDOR Fix

```javascript
// SECURE — Ownership verified
app.get('/api/invoices/:id', authenticate, async (req, res) => {
  const invoice = await db.getInvoice(req.params.id);
  if (!invoice) return res.status(404).json({ error: 'Not found' });
  if (invoice.userId !== req.user.id) {
    return res.status(403).json({ error: 'Forbidden' });
  }
  res.json(invoice);
});
```

## Vulnerable: Missing Function-Level Authorization (Python)

```python
# VULNERABLE — No role check
@app.route('/admin/users', methods=['DELETE'])
@login_required
def delete_user():
    user_id = request.json['user_id']
    db.delete_user(user_id)  # Any logged-in user can delete
    return jsonify({"status": "deleted"})
```

## Secure: Function-Level Authorization

```python
# SECURE — Role verified
@app.route('/admin/users', methods=['DELETE'])
@login_required
@require_role('admin')
def delete_user():
    user_id = request.json['user_id']
    db.delete_user(user_id)
    audit_log.info(f"User {g.user.id} deleted user {user_id}")
    return jsonify({"status": "deleted"})
```

## How to Test

1. Access resources belonging to other users by changing IDs in URLs/parameters
2. Try admin endpoints with a regular user account
3. Test API endpoints directly (bypass UI restrictions)
4. Check that CORS allows only expected origins
5. Use automated tools: Semgrep rules for missing auth middleware

## Prevention Checklist

- [ ] Deny by default — require explicit grants
- [ ] Enforce access control server-side (never rely on client)
- [ ] Use middleware/decorators for consistent enforcement
- [ ] Log access control failures and alert on patterns
- [ ] Use indirect object references (UUIDs) instead of sequential IDs

## See Also

- hub/owasp-top-10.md
- research/authentication/rbac-patterns.md
- research/api-security/cors-configuration.md
