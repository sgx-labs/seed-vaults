---
title: "PCI DSS Basics for Developers"
tags: [compliance, pci-dss, payments, cardholder-data]
content_type: research
domain: security
---

# PCI DSS Basics for Developers

PCI DSS applies if you store, process, or transmit credit card data. The best strategy: minimize your scope by using a payment processor.

## Scope Reduction Strategy

| Approach | PCI Scope | Effort | Cost |
|----------|-----------|--------|------|
| Stripe/PayPal hosted checkout | Minimal (SAQ A) | Low | Transaction fees |
| Stripe Elements (iframe) | Low (SAQ A-EP) | Medium | Transaction fees |
| Direct card handling | Full PCI DSS | Very High | $50K-$500K+ audit |

**Recommendation:** Use Stripe, PayPal, or Braintree hosted checkout pages. This moves nearly all PCI responsibility to them.

## If You Must Handle Card Data

### The 12 PCI DSS Requirements (v4.0)

1. Install and maintain network security controls
2. Apply secure configurations to all system components
3. Protect stored account data
4. Encrypt transmission of cardholder data
5. Protect all systems and networks from malicious software
6. Develop and maintain secure systems and software
7. Restrict access to system components and cardholder data
8. Identify users and authenticate access
9. Restrict physical access to cardholder data
10. Log and monitor all access to system components
11. Test security of systems and networks regularly
12. Support information security with organizational policies

### What You Can NEVER Store

- Full magnetic stripe data
- CVV/CVC (the 3-4 digit security code)
- PIN or PIN block

### Card Number Storage

```python
# SECURE — If you must store card numbers (avoid this if possible)
# Use tokenization instead of storing actual card numbers

# With Stripe — store token, not card number
customer = stripe.Customer.create(
    email='user@example.com',
    source='tok_visa',  # Token from Stripe.js, not actual card number
)

# Store only the customer/payment method ID
db.store_payment_method(
    user_id=user.id,
    stripe_customer_id=customer.id,
    last_four='4242',  # Safe to store for display
    card_brand='visa',  # Safe to store for display
)
```

### Card Number Masking

If you display card numbers, mask all but the last four digits:
```
Display: **** **** **** 4242
```

Never log, email, or display the full card number.

## Minimal PCI Checklist (SAQ A)

If using hosted checkout (Stripe Checkout, PayPal):

- [ ] All payment pages served over HTTPS
- [ ] No card data touches your servers
- [ ] Payment iframes from PCI-certified provider
- [ ] Quarterly vulnerability scans (if applicable)
- [ ] Annual SAQ A self-assessment
- [ ] Keep confirmation that provider is PCI compliant

## How to Test

1. Verify no card numbers appear in any logs
2. Confirm card data never hits your server (check network tab)
3. Verify HTTPS on all payment-related pages
4. Check that CVV is never stored anywhere
5. Confirm Stripe/PayPal handles the actual card input

## See Also

- hub/compliance.md
- research/compliance/soc2-basics.md
- research/infrastructure/sensitive-data-logs.md
