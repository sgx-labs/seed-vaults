---
title: "Background Jobs"
tags: [background-jobs, inngest, trigger-dev, bullmq, queue, cron, serverless, async]
content_type: research
domain: typescript-fullstack
---

# Background Jobs

## The Core Idea

Serverless functions (Vercel, Netlify) are request-response only. Work that takes longer than the timeout, needs retries, or should run on a schedule needs a background job system. Three options: Inngest (event-driven, serverless-native), Trigger.dev (code-first jobs), or BullMQ (self-hosted Redis queue).

## Inngest — Serverless-Native

Event-driven. Deploys alongside your Next.js app. No infrastructure to manage.

```typescript
// lib/inngest.ts
import { Inngest } from "inngest";

export const inngest = new Inngest({ id: "my-app" });

// Define a function
export const processOrder = inngest.createFunction(
  {
    id: "process-order",
    retries: 5,
    concurrency: { limit: 10 },
  },
  { event: "order/placed" },
  async ({ event, step }) => {
    // Step 1: Charge payment
    const payment = await step.run("charge-payment", async () => {
      return stripe.paymentIntents.confirm(event.data.paymentIntentId);
    });

    // Step 2: Send confirmation email (retries independently)
    await step.run("send-confirmation", async () => {
      await sendOrderConfirmation(event.data.orderId);
    });

    // Step 3: Update inventory
    await step.run("update-inventory", async () => {
      await updateInventory(event.data.items);
    });
  }
);

// app/api/inngest/route.ts
import { serve } from "inngest/next";
import { inngest, processOrder } from "@/lib/inngest";

export const { GET, POST, PUT } = serve({
  client: inngest,
  functions: [processOrder],
});

// Trigger from anywhere
await inngest.send({
  name: "order/placed",
  data: { orderId: "ord_123", paymentIntentId: "pi_456", items: [...] },
});
```

**Key feature**: Each `step.run()` is independently retried. If email fails, payment isn't re-charged.

## Trigger.dev — Code-First Jobs

Similar to Inngest but with a different API. Open-source, self-hostable.

```typescript
// trigger/process-order.ts
import { task } from "@trigger.dev/sdk/v3";

export const processOrderTask = task({
  id: "process-order",
  retry: { maxAttempts: 5 },
  run: async (payload: { orderId: string }) => {
    const order = await db.order.findUnique({ where: { id: payload.orderId } });
    await chargePayment(order);
    await sendConfirmation(order);
    await updateInventory(order.items);
  },
});

// Trigger from server action
import { processOrderTask } from "@/trigger/process-order";

export async function placeOrder(data: OrderInput) {
  const order = await db.order.create({ data });
  await processOrderTask.trigger({ orderId: order.id });
  return order;
}
```

## Scheduled Jobs (Cron)

```typescript
// Inngest cron
export const dailyCleanup = inngest.createFunction(
  { id: "daily-cleanup" },
  { cron: "0 2 * * *" }, // 2 AM daily
  async ({ step }) => {
    await step.run("delete-expired-sessions", async () => {
      await db.session.deleteMany({
        where: { expiresAt: { lt: new Date() } },
      });
    });

    await step.run("archive-old-logs", async () => {
      // ... archive logic
    });
  }
);

// Vercel Cron (simpler, limited)
// vercel.json
{
  "crons": [
    { "path": "/api/cron/cleanup", "schedule": "0 2 * * *" }
  ]
}

// app/api/cron/cleanup/route.ts
export async function GET(request: Request) {
  // Verify cron secret
  if (request.headers.get("Authorization") !== `Bearer ${process.env.CRON_SECRET}`) {
    return Response.json({ error: "Unauthorized" }, { status: 401 });
  }
  await cleanup();
  return Response.json({ success: true });
}
```

## Comparison

| Factor | Inngest | Trigger.dev | BullMQ |
|--------|---------|-------------|--------|
| Infrastructure | Managed | Managed or self-hosted | Self-hosted (Redis) |
| Serverless | Native | Native | Needs persistent server |
| Step functions | Yes | Yes | Manual |
| Cron | Yes | Yes | Yes |
| Cost | Free tier, then paid | Free tier, then paid | Free (Redis costs) |
| DX | Excellent | Excellent | Good |

## Common Mistakes

1. **Running long tasks in API routes** — they'll timeout on serverless. Use background jobs.
2. **No retry logic** — external services fail. Always configure retries.
3. **Not making jobs idempotent** — retries may re-execute. Use idempotency keys.
4. **Blocking user response on background work** — return immediately, process async.

## See Also

- `research/email-sending-patterns.md` — Email via background jobs
- `research/stripe-integration.md` — Webhook processing with jobs
- `hub/deployment-patterns.md` — Infrastructure for background work
