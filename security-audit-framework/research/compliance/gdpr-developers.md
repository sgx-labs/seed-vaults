---
title: "GDPR for Developers — What Actually Matters"
tags: [compliance, gdpr, privacy, data-protection, ccpa]
content_type: research
domain: security
---

# GDPR for Developers — What Actually Matters

GDPR applies to any company processing data of EU residents. Fines reach 4% of global revenue or 20M EUR. This covers what developers need to implement.

## Core Principles

1. **Lawfulness** — Every data processing needs a legal basis
2. **Purpose limitation** — Collect data only for specified purposes
3. **Data minimization** — Collect only what you need
4. **Accuracy** — Keep data correct and up to date
5. **Storage limitation** — Delete data you no longer need
6. **Integrity & confidentiality** — Protect data technically

## Legal Bases (Pick One Per Processing Activity)

| Basis | When to Use | Example |
|-------|------------|---------|
| Consent | Marketing, analytics, non-essential cookies | Email newsletter signup |
| Contract | Necessary for the service | Processing a purchase |
| Legal obligation | Required by law | Tax records |
| Legitimate interest | Reasonable business purpose (balanced test) | Fraud prevention |

## User Rights (Must Implement)

### Right to Access (Data Export)

```python
# API endpoint for data export
@app.route('/api/my-data', methods=['GET'])
@login_required
def export_my_data():
    user = g.user
    data = {
        'profile': serialize_profile(user),
        'orders': serialize_orders(user.id),
        'activity_log': serialize_activity(user.id),
        'preferences': serialize_preferences(user.id),
    }
    # Return as downloadable JSON
    response = make_response(json.dumps(data, indent=2))
    response.headers['Content-Type'] = 'application/json'
    response.headers['Content-Disposition'] = 'attachment; filename=my-data.json'
    return response
```

### Right to Deletion (Data Erasure)

```python
# Data deletion with audit trail
@app.route('/api/delete-my-account', methods=['POST'])
@login_required
@require_mfa_verification
def delete_account():
    user_id = g.user.id

    # Soft delete first (30-day grace period)
    db.soft_delete_user(user_id)

    # Schedule hard delete after grace period
    schedule_task('hard_delete_user', user_id, delay_days=30)

    # Anonymize data that must be retained (e.g., financial records)
    db.anonymize_financial_records(user_id)

    # Log the deletion request (required for compliance)
    audit_log.info('gdpr.deletion_request',
                   user_id=user_id,  # This is ok - audit trail
                   requested_at=datetime.utcnow().isoformat())

    # Notify third-party processors to delete data
    notify_processors_of_deletion(user_id)

    return jsonify({"message": "Account scheduled for deletion in 30 days"})
```

### Right to Rectification

```python
# Allow users to correct their data
@app.route('/api/my-profile', methods=['PUT'])
@login_required
def update_profile():
    allowed_fields = ['name', 'email', 'address', 'phone']
    updates = {k: v for k, v in request.json.items() if k in allowed_fields}
    db.update_user(g.user.id, updates)
    audit_log.info('gdpr.data_rectified', user_id=g.user.id, fields=list(updates.keys()))
    return jsonify({"status": "updated"})
```

## Data Breach Notification

- **72 hours** to notify the supervisory authority after becoming aware
- Notify affected individuals "without undue delay" if high risk
- Document all breaches (even ones you do not report)

## Privacy by Design Checklist

- [ ] Data inventory: catalog all personal data collected
- [ ] Purpose documentation: why each piece of data is collected
- [ ] Consent mechanism: opt-in for non-essential processing
- [ ] Data export endpoint: users can download their data
- [ ] Data deletion endpoint: users can request account deletion
- [ ] Retention policy: auto-delete data after specified period
- [ ] Encryption: at rest and in transit for all personal data
- [ ] Access controls: document who can access personal data
- [ ] Third-party DPAs: Data Processing Agreements with all vendors

## CCPA/CPRA Differences

| Requirement | GDPR | CCPA/CPRA |
|------------|------|-----------|
| Consent model | Opt-in | Opt-out |
| Right to deletion | Yes | Yes |
| Data portability | Yes | Yes |
| Fines | Up to 4% revenue | Up to $7,500/violation |
| Applies to | EU residents' data | California residents' data |
| Threshold | Any size company | $26.6M+ revenue OR 100K+ consumers |

## How to Test

1. Can you export all data for a specific user?
2. Can you delete all data for a specific user (with audit trail)?
3. Is consent recorded with timestamp and scope?
4. Do you have a data processing inventory?
5. Can you detect and report a breach within 72 hours?

## See Also

- hub/compliance.md
- research/compliance/soc2-basics.md
- research/infrastructure/sensitive-data-logs.md
- research/infrastructure/incident-response.md
