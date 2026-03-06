---
title: "Adding Stripe Payments"
tags: [guide, stripe, payments, subscriptions, checkout, webhooks, step-by-step]
content_type: guide
domain: typescript-fullstack
---

# Adding Stripe Payments

## Step 1: Install and Configure

```bash
pnpm add stripe
```

```bash
# .env.local
STRIPE_SECRET_KEY="sk_test_..."
STRIPE_WEBHOOK_SECRET="whsec_..."
NEXT_PUBLIC_STRIPE_PUBLISHABLE_KEY="pk_test_..."
```

```typescript
// lib/stripe.ts
import Stripe from "stripe";

export const stripe = new Stripe(process.env.STRIPE_SECRET_KEY!, {
  apiVersion: "2024-12-18.acacia",
  typescript: true,
});
```

## Step 2: Create Products in Stripe

Use the Stripe Dashboard or API to create products and prices.

```typescript
// scripts/create-products.ts (run once)
const product = await stripe.products.create({ name: "Pro Plan" });
const price = await stripe.prices.create({
  product: product.id,
  unit_amount: 2000, // $20.00
  currency: "usd",
  recurring: { interval: "month" },
});
console.log(`Price ID: ${price.id}`); // Save this
```

## Step 3: Add Database Fields

```prisma
model User {
  id               String   @id @default(cuid())
  // ... existing fields
  stripeCustomerId String?  @unique
  subscriptionId   String?
  plan             String   @default("free")
  subscriptionEndsAt DateTime?
}
```

```bash
npx prisma migrate dev --name add-stripe-fields
```

## Step 4: Create Checkout API Route

```typescript
// app/api/checkout/route.ts
import { stripe } from "@/lib/stripe";
import { auth } from "@/auth";

export async function POST(request: Request) {
  const session = await auth();
  if (!session?.user) {
    return Response.json({ error: "Unauthorized" }, { status: 401 });
  }

  const { priceId } = await request.json();

  const checkout = await stripe.checkout.sessions.create({
    mode: "subscription",
    customer_email: session.user.email!,
    line_items: [{ price: priceId, quantity: 1 }],
    success_url: `${process.env.NEXT_PUBLIC_APP_URL}/billing?success=true`,
    cancel_url: `${process.env.NEXT_PUBLIC_APP_URL}/billing`,
    metadata: { userId: session.user.id },
  });

  return Response.json({ url: checkout.url });
}
```

## Step 5: Create Webhook Handler

See `research/stripe-integration.md` for the full webhook handler.

## Step 6: Create Billing Portal Route

```typescript
// app/api/billing/portal/route.ts
import { stripe } from "@/lib/stripe";
import { auth } from "@/auth";
import { db } from "@/lib/db";

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

## Step 7: Create Pricing UI

```typescript
"use client";

export function PricingCard({ priceId, name, price }: Props) {
  const [loading, setLoading] = useState(false);

  async function handleSubscribe() {
    setLoading(true);
    const res = await fetch("/api/checkout", {
      method: "POST",
      body: JSON.stringify({ priceId }),
    });
    const { url } = await res.json();
    window.location.href = url;
  }

  return (
    <div className="border rounded-lg p-6">
      <h3>{name}</h3>
      <p className="text-3xl font-bold">${price}/mo</p>
      <button onClick={handleSubscribe} disabled={loading}>
        {loading ? "Loading..." : "Subscribe"}
      </button>
    </div>
  );
}
```

## Step 8: Test with Stripe CLI

```bash
stripe listen --forward-to localhost:3000/api/webhooks/stripe
# Copy the webhook signing secret to STRIPE_WEBHOOK_SECRET
```

## Step 9: Gate Features by Plan

```typescript
// lib/auth-utils.ts
export async function requirePlan(plan: "pro" | "enterprise") {
  const session = await auth();
  if (!session?.user) redirect("/login");

  const user = await db.user.findUnique({ where: { id: session.user.id } });
  if (user?.plan !== plan) redirect("/billing?upgrade=true");

  return user;
}
```

## See Also

- `research/stripe-integration.md` — Deep dive on webhooks, metered billing
- `hub/api-route-patterns.md` — API route security
- `hub/authentication-patterns.md` — Auth integration
