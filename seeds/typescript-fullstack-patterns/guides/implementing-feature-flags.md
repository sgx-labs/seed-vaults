---
title: "Implementing Feature Flags"
tags: [guide, feature-flags, feature-toggles, posthog, vercel, a-b-testing]
content_type: guide
domain: typescript-fullstack
---

# Implementing Feature Flags

## Why Feature Flags?

- Deploy code without releasing features
- A/B test new features on a subset of users
- Kill switch for broken features without redeploying
- Gradual rollouts (1% -> 10% -> 50% -> 100%)

## Option 1: Simple Database Flags

For small teams. Store flags in the database, cache aggressively.

```typescript
// lib/feature-flags.ts
import { db } from "@/lib/db";
import { unstable_cache } from "next/cache";

type FeatureFlag = "new-dashboard" | "ai-assistant" | "dark-mode" | "beta-pricing";

const getFlags = unstable_cache(
  async () => {
    const flags = await db.featureFlag.findMany({ where: { enabled: true } });
    return new Set(flags.map((f) => f.name));
  },
  ["feature-flags"],
  { revalidate: 60 } // Refresh every 60 seconds
);

export async function isEnabled(flag: FeatureFlag): Promise<boolean> {
  const flags = await getFlags();
  return flags.has(flag);
}

// Usage in Server Component
export default async function Dashboard() {
  const showNewDashboard = await isEnabled("new-dashboard");
  return showNewDashboard ? <NewDashboard /> : <OldDashboard />;
}
```

## Option 2: PostHog Feature Flags

Full-featured: percentage rollouts, user targeting, A/B testing, analytics.

```typescript
// lib/posthog-server.ts
import { PostHog } from "posthog-node";

const posthog = new PostHog(process.env.POSTHOG_API_KEY!, {
  host: process.env.NEXT_PUBLIC_POSTHOG_HOST,
});

export async function isFeatureEnabled(
  flag: string,
  userId: string
): Promise<boolean> {
  return posthog.isFeatureEnabled(flag, userId) ?? false;
}

// Usage in Server Component
export default async function PricingPage() {
  const session = await auth();
  const showNewPricing = session
    ? await isFeatureEnabled("new-pricing", session.user.id)
    : false;

  return showNewPricing ? <NewPricing /> : <CurrentPricing />;
}
```

```typescript
// Client-side flag check
"use client";

import { useFeatureFlagEnabled } from "posthog-js/react";

export function FeaturePreview() {
  const showBeta = useFeatureFlagEnabled("beta-feature");

  if (!showBeta) return null;
  return <BetaBanner />;
}
```

## Option 3: Vercel Edge Config

Ultra-fast reads from Vercel's global edge network.

```typescript
import { get } from "@vercel/edge-config";

export async function isEnabled(flag: string): Promise<boolean> {
  return (await get<boolean>(flag)) ?? false;
}

// Works in middleware (edge runtime)
export async function middleware(request: NextRequest) {
  const maintenanceMode = await get<boolean>("maintenance-mode");
  if (maintenanceMode) {
    return NextResponse.rewrite(new URL("/maintenance", request.url));
  }
}
```

## Cleanup Pattern

Feature flags are temporary. Schedule removal.

```typescript
// Add expiration dates and comments
/**
 * @flag new-dashboard
 * @created 2026-01-15
 * @owner @alice
 * @remove-after 2026-03-15
 * @description Redesigned dashboard with new chart library
 */
const showNewDashboard = await isEnabled("new-dashboard");
```

## See Also

- `research/analytics-observability.md` — PostHog setup
- `entities/common-middleware-patterns.md` — Flag checks in middleware
- `hub/deployment-patterns.md` — Feature flags in deployment
