---
title: "Stripe — Current State"
tags: [stripe, payments, billing, entity, platform]
content_type: entity
domain: business
pinned: true
---

# Stripe — Current State

## What It Is

Payment infrastructure for internet businesses. The default choice for SaaS billing, subscriptions, and one-time payments. Most SaaS boilerplates integrate Stripe out of the box.

## Pricing (2025-2026)

- **Standard rate:** 2.9% + $0.30 per successful card charge
- **Volume discounts:** Available above $1M/year processing
- **No monthly fees** for standard accounts
- **Stripe Tax:** Additional 0.5% per transaction for automated tax calculation

## Key Products for Indie Hackers

| Product | Use case |
|---------|---------|
| Checkout | Hosted payment page (fastest to integrate) |
| Billing | Subscription management with invoicing |
| Payment Links | No-code payment pages (shareable URL) |
| Customer Portal | Self-serve subscription management |
| Tax | Automated sales tax/VAT calculation |

## Stripe vs. Lemon Squeezy vs. Polar

| Feature | Stripe | Lemon Squeezy | Polar |
|---------|--------|---------------|-------|
| Fee | 2.9% + $0.30 | 5% + $0.50 | 5% |
| Tax handling | Add-on (Stripe Tax) | Included (MoR) | Included |
| Setup complexity | Medium | Low | Low |
| Merchant of Record | No (you handle tax) | Yes | Yes |
| Best for | Control + scale | Simplicity + tax | Open source + dev tools |

**Lemon Squeezy update:** Stripe acquired Lemon Squeezy in July 2024. Now offers "Stripe Managed Payments" combining LS simplicity with Stripe infrastructure.

## Quick Decision

- **Under $100K MRR and want simplicity:** Lemon Squeezy
- **Want full control and plan to scale:** Stripe direct
- **Open source or dev tools:** Polar
- **Selling globally and worried about tax:** Lemon Squeezy or Paddle

## Setup Checklist

1. Create Stripe account (takes minutes)
2. Add bank account for payouts
3. Create products and prices in dashboard
4. Integrate Checkout or Payment Links
5. Set up webhook for subscription events
6. Add Customer Portal for self-serve management
7. Enable Stripe Tax if selling globally

## Last Updated

February 2026
