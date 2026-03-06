---
title: "Stripe Integration Patterns"
tags: [stripe, payments, subscriptions, webhooks, billing, checkout, metered-billing]
content_type: research
domain: typescript-fullstack
---

# Stripe Integration Patterns

## The Core Idea

Stripe integration has three parts: creating checkout sessions (payments), handling webhooks (event processing), and managing subscriptions (billing lifecycle). The webhook handler is the most important -- it's the source of truth for payment state, not your API calls.

## Checkout Session — One-Time Payments

```typescript
// app/api/checkout/route.ts
import Stripe from "stripe";

const stripe = new Stripe(process.env.STRIPE_SECRET_KEY!);

export async function POST(request: Request) {
  const { priceId } = await request.json();
  const session = await auth();
  if (!session?.user) return Response.json({ error: "Unauthorized" }, { status: 401 });

  const checkout = await stripe.checkout.sessions.create({
    mode: "subscription",
    customer_email: session.user.email!,
    line_items: [{ price: priceId, quantity: 1 }],
    success_url: `${process.env.NEXT_PUBLIC_APP_URL}/billing?success=true`,
    cancel_url: `${process.env.NEXT_PUBLIC_APP_URL}/billing?canceled=true`,
    metadata: { userId: session.user.id },
  });

  return Response.json({ url: checkout.url });
}
```

```typescript
// Client
"use client";

async function handleSubscribe(priceId: string) {
  const res = await fetch("/api/checkout", {
    method: "POST",
    body: JSON.stringify({ priceId }),
  });
  const { url } = await res.json();
  window.location.href = url; // Redirect to Stripe Checkout
}
```

## Webhook Handler — Source of Truth

```typescript
// app/api/webhooks/stripe/route.ts
import Stripe from "stripe";

const stripe = new Stripe(process.env.STRIPE_SECRET_KEY!);

export async function POST(request: Request) {
  const body = await request.text();
  const signature = request.headers.get("stripe-signature")!;

  let event: Stripe.Event;
  try {
    event = stripe.webhooks.constructEvent(
      body,
      signature,
      process.env.STRIPE_WEBHOOK_SECRET!
    );
  } catch (err) {
    console.error("Webhook signature verification failed:", err);
    return Response.json({ error: "Invalid signature" }, { status: 400 });
  }

  switch (event.type) {
    case "checkout.session.completed": {
      const session = event.data.object as Stripe.Checkout.Session;
      await db.user.update({
        where: { id: session.metadata!.userId },
        data: {
          stripeCustomerId: session.customer as string,
          subscriptionId: session.subscription as string,
          plan: "pro",
        },
      });
      break;
    }

    case "customer.subscription.updated": {
      const subscription = event.data.object as Stripe.Subscription;
      await db.user.updateMany({
        where: { stripeCustomerId: subscription.customer as string },
        data: {
          plan: subscription.status === "active" ? "pro" : "free",
          subscriptionEndsAt: new Date(subscription.current_period_end * 1000),
        },
      });
      break;
    }

    case "customer.subscription.deleted": {
      const subscription = event.data.object as Stripe.Subscription;
      await db.user.updateMany({
        where: { stripeCustomerId: subscription.customer as string },
        data: { plan: "free", subscriptionId: null },
      });
      break;
    }

    case "invoice.payment_failed": {
      const invoice = event.data.object as Stripe.Invoice;
      // Notify user, downgrade after grace period
      await sendPaymentFailedEmail(invoice.customer as string);
      break;
    }
  }

  return Response.json({ received: true });
}
```

**Critical**: Always return 200 to Stripe, even if your processing fails. Otherwise Stripe retries and may cause duplicate processing. Handle errors internally.

## Customer Portal — Self-Service Billing

```typescript
// app/api/billing/portal/route.ts
export async function POST() {
  const session = await auth();
  if (!session?.user) return Response.json({ error: "Unauthorized" }, { status: 401 });

  const user = await db.user.findUnique({ where: { id: session.user.id } });
  if (!user?.stripeCustomerId) {
    return Response.json({ error: "No billing account" }, { status: 400 });
  }

  const portal = await stripe.billingPortal.sessions.create({
    customer: user.stripeCustomerId,
    return_url: `${process.env.NEXT_PUBLIC_APP_URL}/billing`,
  });

  return Response.json({ url: portal.url });
}
```

## Metered Billing

```typescript
// Report usage to Stripe
async function reportUsage(userId: string, quantity: number) {
  const user = await db.user.findUnique({ where: { id: userId } });
  if (!user?.subscriptionId) return;

  const subscription = await stripe.subscriptions.retrieve(user.subscriptionId);
  const meteredItem = subscription.items.data.find(
    (item) => item.price.recurring?.usage_type === "metered"
  );

  if (meteredItem) {
    await stripe.subscriptionItems.createUsageRecord(meteredItem.id, {
      quantity,
      timestamp: Math.floor(Date.now() / 1000),
      action: "increment",
    });
  }
}
```

## Common Mistakes

1. **Not verifying webhook signatures** — anyone can send POST requests to your webhook URL
2. **Processing webhooks synchronously** — for slow operations, acknowledge and process in a background job
3. **Not handling duplicate events** — Stripe may send the same event multiple times. Make handlers idempotent.
4. **Checking payment status via API instead of webhooks** — webhooks are the source of truth
5. **Not testing with Stripe CLI** — `stripe listen --forward-to localhost:3000/api/webhooks/stripe`

## See Also

- `hub/api-route-patterns.md` — Route handler patterns
- `research/background-jobs.md` — Processing webhooks with jobs
- `guides/adding-stripe-payments.md` — Step-by-step guide
