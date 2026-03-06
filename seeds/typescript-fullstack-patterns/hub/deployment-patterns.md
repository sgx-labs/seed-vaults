---
title: "Deployment Patterns"
tags: [deployment, vercel, railway, docker, edge-functions, preview, ci-cd, infrastructure]
content_type: hub
domain: typescript-fullstack
---

# Deployment Patterns

## Overview

Next.js apps can deploy to Vercel (zero-config), Railway/Fly.io (container-based), or self-hosted Docker. Each has tradeoffs around cost, control, and complexity. This note covers production deployment patterns for each target.

## Vercel — The Path of Least Resistance

Zero-config deployment for Next.js. Edge functions, automatic preview deployments, analytics built in.

```json
// vercel.json (usually not needed — defaults are good)
{
  "framework": "nextjs",
  "regions": ["iad1"],
  "crons": [
    { "path": "/api/cron/cleanup", "schedule": "0 2 * * *" }
  ]
}
```

**Environment variables**: Set in Vercel dashboard per environment (Production, Preview, Development).

**When to use**: Teams that want zero DevOps, need preview deployments, and are OK with Vercel pricing.

**When NOT to use**: Budget-constrained (costs scale with usage), need long-running processes, need custom infrastructure.

**Footgun**: Serverless function timeout is 10s on Hobby, 60s on Pro. Long database queries or file processing will time out.

## Railway / Fly.io — Container-Based

Full control with managed infrastructure. Run long-running processes, WebSocket servers, cron jobs.

```dockerfile
# Dockerfile — multi-stage for small images
FROM node:20-alpine AS base
RUN corepack enable

FROM base AS deps
WORKDIR /app
COPY package.json pnpm-lock.yaml ./
RUN pnpm install --frozen-lockfile

FROM base AS builder
WORKDIR /app
COPY --from=deps /app/node_modules ./node_modules
COPY . .
RUN pnpm build

FROM base AS runner
WORKDIR /app
ENV NODE_ENV=production

RUN addgroup --system --gid 1001 nodejs
RUN adduser --system --uid 1001 nextjs

COPY --from=builder /app/public ./public
COPY --from=builder --chown=nextjs:nodejs /app/.next/standalone ./
COPY --from=builder --chown=nextjs:nodejs /app/.next/static ./.next/static

USER nextjs
EXPOSE 3000
ENV PORT=3000

CMD ["node", "server.js"]
```

```typescript
// next.config.ts — required for standalone output
const nextConfig = {
  output: "standalone",
};
export default nextConfig;
```

## Edge Functions

Run code at the CDN edge for low-latency responses. Limited runtime (no Node.js APIs, no native modules).

```typescript
// app/api/geo/route.ts
export const runtime = "edge";

export async function GET(request: Request) {
  const country = request.headers.get("x-vercel-ip-country") ?? "unknown";
  return Response.json({ country, timestamp: Date.now() });
}

// middleware.ts — always runs on edge
import { NextResponse } from "next/server";

export function middleware(request: NextRequest) {
  const country = request.geo?.country ?? "US";

  // Redirect based on geo
  if (country === "DE" && !request.nextUrl.pathname.startsWith("/de")) {
    return NextResponse.redirect(new URL("/de" + request.nextUrl.pathname, request.url));
  }

  return NextResponse.next();
}
```

**Footgun**: Edge runtime cannot use Node.js APIs (`fs`, `crypto.createHash`, `Buffer`). Check your dependencies.

## Environment Variables

```typescript
// env.ts — validate at build time with t3-env
import { createEnv } from "@t3-oss/env-nextjs";
import { z } from "zod";

export const env = createEnv({
  server: {
    DATABASE_URL: z.string().url(),
    AUTH_SECRET: z.string().min(32),
    STRIPE_SECRET_KEY: z.string().startsWith("sk_"),
  },
  client: {
    NEXT_PUBLIC_APP_URL: z.string().url(),
    NEXT_PUBLIC_POSTHOG_KEY: z.string().optional(),
  },
  runtimeEnv: {
    DATABASE_URL: process.env.DATABASE_URL,
    AUTH_SECRET: process.env.AUTH_SECRET,
    STRIPE_SECRET_KEY: process.env.STRIPE_SECRET_KEY,
    NEXT_PUBLIC_APP_URL: process.env.NEXT_PUBLIC_APP_URL,
    NEXT_PUBLIC_POSTHOG_KEY: process.env.NEXT_PUBLIC_POSTHOG_KEY,
  },
});
```

## Health Check Endpoint

```typescript
// app/api/health/route.ts
import { db } from "@/lib/db";

export async function GET() {
  try {
    await db.$queryRaw`SELECT 1`;
    return Response.json({
      status: "healthy",
      timestamp: new Date().toISOString(),
      version: process.env.COMMIT_SHA ?? "unknown",
    });
  } catch {
    return Response.json(
      { status: "unhealthy", error: "Database connection failed" },
      { status: 503 }
    );
  }
}
```

## Comparison

| Factor | Vercel | Railway | Docker (self-hosted) |
|--------|--------|---------|---------------------|
| Setup time | Minutes | 30 min | Hours |
| Preview deploys | Automatic | Manual/CD | DIY |
| Long processes | No (timeout) | Yes | Yes |
| WebSockets | Limited | Yes | Yes |
| Cost control | Low | Medium | High |
| Vendor lock-in | Medium | Low | None |
| Edge functions | Native | No | No |

## See Also

- `guides/setting-up-ci-cd.md` — GitHub Actions for Next.js
- `research/edge-runtime-limitations.md` — Edge runtime deep dive
- `entities/env-variables-patterns.md` — Environment variable management
- `hub/performance-patterns.md` — Production performance
