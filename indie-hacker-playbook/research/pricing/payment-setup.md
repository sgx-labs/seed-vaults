---
title: "Payment Setup — Stripe, Lemon Squeezy, and Polar"
tags: [pricing, payments, stripe, lemon-squeezy, polar, setup]
content_type: research
domain: business
---

# Payment Setup — Stripe, Lemon Squeezy, and Polar

## The Quick Decision

| Situation | Best choice |
|-----------|------------|
| Want full control, plan to scale | Stripe |
| Solo founder, want simplicity + tax handling | Lemon Squeezy |
| Open source or developer tool | Polar |
| Selling globally, worried about tax | Lemon Squeezy or Paddle |
| Using a SaaS boilerplate | Whatever it integrates with (usually Stripe) |

## Option 1: Stripe (Most Flexible)

**Fee:** 2.9% + $0.30 per transaction

**Pros:**
- Industry standard, works everywhere
- Excellent documentation and developer experience
- Supports every payment model (subscriptions, one-time, metered, usage-based)
- Customer Portal for self-serve subscription management
- Stripe Checkout = hosted payment page (zero custom UI needed)

**Cons:**
- You handle sales tax/VAT compliance
- More setup work than alternatives
- Dashboard can be overwhelming

**Fastest Setup Path:**
1. Create a Stripe account (5 minutes)
2. Create a Product with Price in the dashboard
3. Use Stripe Payment Links or Checkout Sessions
4. Add webhook handler for subscription events
5. Enable Customer Portal for self-serve management

**Tax Solution:** Add Stripe Tax (0.5% extra per transaction) for automated tax calculation. Or use Lemon Squeezy instead.

## Option 2: Lemon Squeezy (Simplest)

**Fee:** 5% + $0.50 per transaction

**Pros:**
- Merchant of Record (MoR) — they handle all tax compliance globally
- Dead simple setup — create product, get link, done
- Built-in affiliate management
- Checkout overlay or hosted page
- Acquired by Stripe in 2024 (stable, well-funded)

**Cons:**
- Higher fees than Stripe
- Less customization than Stripe
- Fewer integration options

**Fastest Setup Path:**
1. Create a Lemon Squeezy account
2. Create a Store, then a Product with pricing
3. Copy the checkout link
4. Put it on your landing page
5. Done. They handle tax, billing, and receipts.

## Option 3: Polar (For Open Source / Dev Tools)

**Fee:** 5% per transaction

**Pros:**
- Built specifically for developers and open source
- GitHub integration (sponsor-like experience)
- Supports subscriptions, one-time, pay-what-you-want
- Merchant of Record (handles tax)
- Growing developer community

**Cons:**
- Newer, smaller ecosystem
- Less suited for non-developer products
- Fewer integrations

## Setup Checklist (Any Provider)

1. [ ] Create account and verify identity
2. [ ] Add bank account for payouts
3. [ ] Create products with monthly and annual prices
4. [ ] Set up checkout page or payment link
5. [ ] Test with a real card (Stripe test cards for Stripe)
6. [ ] Set up webhook/notification for payment events
7. [ ] Handle subscription lifecycle: created, updated, cancelled, failed payment
8. [ ] Set up dunning (failed payment retry) — most providers do this automatically
9. [ ] Add receipt/invoice emails (most providers do this automatically)
10. [ ] Test the full flow: signup, pay, use product, cancel, refund

## Revenue Split at Different Scales

| MRR | Stripe cost (2.9%) | LS cost (5%) | Difference |
|-----|-------|--------|------------|
| $1K | $29 | $50 | $21/mo |
| $5K | $145 | $250 | $105/mo |
| $10K | $290 | $500 | $210/mo |
| $50K | $1,450 | $2,500 | $1,050/mo |

At low MRR, the convenience of Lemon Squeezy outweighs the fee difference. At $10K+ MRR, consider switching to Stripe for savings.
