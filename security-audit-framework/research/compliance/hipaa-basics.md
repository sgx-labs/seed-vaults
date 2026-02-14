---
title: "HIPAA Basics for Developers"
tags: [compliance, hipaa, healthcare, phi, encryption]
content_type: research
domain: security
---

# HIPAA Basics for Developers

HIPAA applies if you handle Protected Health Information (PHI). This includes health data, insurance data, and payment for healthcare services.

## Who Needs HIPAA Compliance?

- **Covered Entities**: Healthcare providers, health plans, clearinghouses
- **Business Associates**: Any vendor that handles PHI on behalf of a covered entity (this is you if you are building SaaS for healthcare)

## What is PHI?

Any individually identifiable health information, including:
- Name + diagnosis
- Email + medication
- IP address + health condition
- Any combination of identifiers + health data

**The 18 HIPAA Identifiers:**
Names, addresses, dates (except year), phone numbers, email, SSN, medical record numbers, health plan IDs, account numbers, certificate/license numbers, vehicle identifiers, device identifiers, URLs, IP addresses, biometric identifiers, photos, any unique identifier.

## Technical Safeguards (What Developers Build)

### Access Control

```python
# SECURE — Role-based PHI access with audit logging
@app.route('/api/patients/<patient_id>/records')
@login_required
@require_permission('phi', 'read')
def get_patient_records(patient_id):
    # Log every PHI access (required by HIPAA)
    audit_log.info('hipaa.phi_accessed',
                   accessor_id=g.user.id,
                   accessor_role=g.user.role,
                   patient_id=patient_id,
                   timestamp=datetime.utcnow().isoformat(),
                   access_reason=request.headers.get('X-Access-Reason'))

    records = db.get_patient_records(patient_id)
    return jsonify(records)
```

### Encryption Requirements

| Data State | Requirement | Implementation |
|-----------|-------------|----------------|
| At rest | Required | AES-256 database encryption, encrypted backups |
| In transit | Required | TLS 1.2+ for all connections |
| Backups | Required | Encrypted with separate key |
| Removable media | Required | Full-disk encryption |

### Audit Trail

HIPAA requires logging of:
- Who accessed what PHI
- When it was accessed
- What was done (read, modify, delete)
- From where (IP, device)

**Retention: Minimum 6 years** for audit logs.

### Automatic Logoff

```javascript
// SECURE — HIPAA-compliant session timeout
const SESSION_TIMEOUT = 15 * 60 * 1000; // 15 minutes

// Server-side
app.use((req, res, next) => {
  if (req.session && req.session.lastActivity) {
    const elapsed = Date.now() - req.session.lastActivity;
    if (elapsed > SESSION_TIMEOUT) {
      req.session.destroy();
      return res.status(401).json({ error: 'Session expired' });
    }
  }
  if (req.session) {
    req.session.lastActivity = Date.now();
  }
  next();
});
```

## Business Associate Agreement (BAA)

You MUST have a BAA with:
- Cloud providers (AWS, GCP, Azure all offer BAAs)
- Any SaaS vendor that touches PHI
- Subcontractors who handle PHI

**No BAA = HIPAA violation**, regardless of your technical controls.

## Common Violations

- PHI in application logs (names, diagnoses in debug output)
- PHI in error messages sent to clients
- Unencrypted PHI backups
- Missing access logs for PHI
- Shared credentials for PHI access
- PHI in development/test environments (use synthetic data)

## Minimum Viable HIPAA

1. **Encryption** everywhere (rest + transit)
2. **Access control** with role-based permissions
3. **Audit logging** of all PHI access
4. **BAAs** with all vendors handling PHI
5. **Automatic session timeout** (15 minutes)
6. **Employee training** (annual, documented)
7. **Incident response plan** (documented, tested)
8. **Risk assessment** (annual, documented)

## How to Test

1. Check all PHI is encrypted at rest (database, file storage, backups)
2. Verify TLS 1.2+ on all connections handling PHI
3. Confirm audit logs capture who/what/when for PHI access
4. Verify session timeout works (15 minutes for clinical apps)
5. Check that PHI does not appear in application logs

## See Also

- hub/compliance.md
- research/compliance/soc2-basics.md
- research/infrastructure/sensitive-data-logs.md
- research/authentication/rbac-patterns.md
