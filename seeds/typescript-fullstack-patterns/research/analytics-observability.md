---
title: "Analytics and Observability"
tags: [analytics, observability, posthog, vercel-analytics, opentelemetry, logging, monitoring, sentry]
content_type: research
domain: typescript-fullstack
---

# Analytics and Observability

## The Core Idea

Observability has three pillars: logs (what happened), metrics (how much), and traces (how long). Product analytics adds a fourth: user behavior (who did what). This note covers production setups for each.

## Product Analytics — PostHog

Open-source, self-hostable. Feature flags, session replay, funnels, A/B testing.

```typescript
// lib/posthog.ts
"use client";

import posthog from "posthog-js";
import { PostHogProvider as PHProvider } from "posthog-js/react";

export function PostHogProvider({ children }: { children: React.ReactNode }) {
  if (typeof window !== "undefined" && !posthog.__loaded) {
    posthog.init(process.env.NEXT_PUBLIC_POSTHOG_KEY!, {
      api_host: process.env.NEXT_PUBLIC_POSTHOG_HOST ?? "https://us.i.posthog.com",
      capture_pageview: false, // Manual capture for App Router
    });
  }
  return <PHProvider client={posthog}>{children}</PHProvider>;
}
```

```typescript
// Track page views in App Router
"use client";

import { usePathname, useSearchParams } from "next/navigation";
import { usePostHog } from "posthog-js/react";
import { useEffect } from "react";

export function PostHogPageView() {
  const pathname = usePathname();
  const searchParams = useSearchParams();
  const posthog = usePostHog();

  useEffect(() => {
    if (pathname) {
      let url = window.origin + pathname;
      if (searchParams.toString()) url += `?${searchParams.toString()}`;
      posthog.capture("$pageview", { $current_url: url });
    }
  }, [pathname, searchParams, posthog]);

  return null;
}
```

```typescript
// Track custom events
import { usePostHog } from "posthog-js/react";

function SubscribeButton({ plan }: { plan: string }) {
  const posthog = usePostHog();

  return (
    <button
      onClick={() => {
        posthog.capture("subscription_started", { plan, price: getPlanPrice(plan) });
        handleSubscribe(plan);
      }}
    >
      Subscribe
    </button>
  );
}
```

## Error Tracking — Sentry

Catch and report runtime errors with context.

```typescript
// sentry.client.config.ts
import * as Sentry from "@sentry/nextjs";

Sentry.init({
  dsn: process.env.NEXT_PUBLIC_SENTRY_DSN,
  tracesSampleRate: 0.1, // 10% of transactions for performance monitoring
  replaysSessionSampleRate: 0.01,
  replaysOnErrorSampleRate: 1.0,
});

// Manual error capture
try {
  await riskyOperation();
} catch (error) {
  Sentry.captureException(error, {
    tags: { operation: "payment-processing" },
    extra: { userId, orderId },
  });
}
```

## Structured Logging

```typescript
// lib/logger.ts
type LogLevel = "debug" | "info" | "warn" | "error";

interface LogEntry {
  level: LogLevel;
  message: string;
  timestamp: string;
  [key: string]: unknown;
}

function log(level: LogLevel, message: string, meta?: Record<string, unknown>) {
  const entry: LogEntry = {
    level,
    message,
    timestamp: new Date().toISOString(),
    ...meta,
  };

  // JSON structured logging for log aggregation tools
  console[level === "error" ? "error" : "log"](JSON.stringify(entry));
}

export const logger = {
  debug: (msg: string, meta?: Record<string, unknown>) => log("debug", msg, meta),
  info: (msg: string, meta?: Record<string, unknown>) => log("info", msg, meta),
  warn: (msg: string, meta?: Record<string, unknown>) => log("warn", msg, meta),
  error: (msg: string, meta?: Record<string, unknown>) => log("error", msg, meta),
};

// Usage
logger.info("Order created", { orderId: "ord_123", userId: "usr_456", total: 99.99 });
logger.error("Payment failed", { orderId: "ord_123", error: err.message });
```

## OpenTelemetry — Distributed Tracing

```typescript
// instrumentation.ts (Next.js instrumentation hook)
export async function register() {
  if (process.env.NEXT_RUNTIME === "nodejs") {
    const { NodeSDK } = await import("@opentelemetry/sdk-node");
    const { OTLPTraceExporter } = await import("@opentelemetry/exporter-trace-otlp-http");

    const sdk = new NodeSDK({
      traceExporter: new OTLPTraceExporter({
        url: process.env.OTEL_EXPORTER_OTLP_ENDPOINT,
      }),
    });

    sdk.start();
  }
}
```

## What to Track

| Category | Examples | Tool |
|----------|---------|------|
| User behavior | Page views, button clicks, funnels | PostHog |
| Errors | Unhandled exceptions, API errors | Sentry |
| Performance | Response times, Core Web Vitals | Vercel Analytics, OpenTelemetry |
| Business metrics | Signups, conversions, revenue | PostHog, custom dashboard |
| Infrastructure | CPU, memory, request count | Vercel/Railway dashboards |

## See Also

- `hub/performance-patterns.md` — Core Web Vitals monitoring
- `hub/error-handling.md` — Error tracking integration
- `hub/deployment-patterns.md` — Platform monitoring
