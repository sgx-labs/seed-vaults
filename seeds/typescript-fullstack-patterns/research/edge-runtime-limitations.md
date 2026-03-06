---
title: "Edge Runtime Limitations and Workarounds"
tags: [edge, runtime, vercel, cloudflare, limitations, node-apis, workarounds]
content_type: research
domain: typescript-fullstack
---

# Edge Runtime Limitations and Workarounds

## The Core Idea

Edge functions run on CDN nodes close to users (~50ms vs ~200ms for regional servers). But they run V8 isolates, not Node.js. This means no `fs`, no native modules, no long-running processes, and limited memory. Knowing what works and what doesn't saves hours of debugging.

## What's NOT Available

| API | Status | Workaround |
|-----|--------|------------|
| `fs` (filesystem) | Not available | Use external storage (S3, R2) |
| `child_process` | Not available | Use external services |
| `crypto.createHash` | Not available | Use `crypto.subtle` (Web Crypto API) |
| `Buffer` | Limited | Use `Uint8Array` or polyfill |
| `process.env` | Available (read-only) | Normal usage |
| `fetch` | Available | Normal usage |
| `setTimeout` | Available (with limits) | Execution timeout still applies |
| Native modules (sharp, bcrypt) | Not available | Use WASM alternatives |

## Database Access on Edge

```typescript
// DON'T — regular Prisma client won't work
import { PrismaClient } from "@prisma/client";
const db = new PrismaClient(); // Fails on edge

// DO — use Prisma with Accelerate
import { PrismaClient } from "@prisma/client/edge";
import { withAccelerate } from "@prisma/extension-accelerate";
const db = new PrismaClient().$extends(withAccelerate());

// DO — use Drizzle with serverless driver
import { neon } from "@neondatabase/serverless";
import { drizzle } from "drizzle-orm/neon-http";
const db = drizzle(neon(process.env.DATABASE_URL!));

// DO — use Supabase with edge-compatible client
import { createClient } from "@supabase/supabase-js";
const supabase = createClient(url, key);
```

## Crypto on Edge

```typescript
// DON'T
import crypto from "crypto";
const hash = crypto.createHash("sha256").update(data).digest("hex");

// DO — Web Crypto API
async function sha256(data: string): Promise<string> {
  const encoder = new TextEncoder();
  const buffer = await crypto.subtle.digest("SHA-256", encoder.encode(data));
  return Array.from(new Uint8Array(buffer))
    .map((b) => b.toString(16).padStart(2, "0"))
    .join("");
}

// DON'T — bcrypt (native module)
import bcrypt from "bcrypt";

// DO — use bcryptjs (pure JS) or @noble/hashes
import bcryptjs from "bcryptjs";
const hash = await bcryptjs.hash(password, 10);
```

## Execution Limits

| Platform | Max Duration | Max Memory | Max Size |
|----------|-------------|------------|----------|
| Vercel Edge | 30s (Hobby) / 5min (Pro) | 128MB | 4MB |
| Cloudflare Workers | 30s (free) / 15min (paid) | 128MB | 10MB |
| Next.js Middleware | ~25s | 128MB | 4MB |

## When to Use Edge vs Node.js Runtime

```typescript
// Edge — fast, stateless, simple logic
export const runtime = "edge";

export async function GET(request: Request) {
  const country = request.headers.get("x-vercel-ip-country");
  return Response.json({ greeting: getLocalGreeting(country) });
}

// Node.js — complex logic, native modules, long operations
export const runtime = "nodejs";

export async function POST(request: Request) {
  const file = await request.formData();
  const buffer = await file.get("image").arrayBuffer();
  const processed = await sharp(Buffer.from(buffer)).resize(800).webp().toBuffer();
  // ... upload to S3
}
```

**Decision rule**: Use edge for simple request/response transformations (auth, redirects, geo-routing, A/B testing). Use Node.js for everything else.

## Middleware — Always Edge

```typescript
// middleware.ts — always runs on edge
// Keep it lightweight: auth checks, redirects, headers

import { NextResponse } from "next/server";

export function middleware(request: NextRequest) {
  // Simple, fast operations only
  const response = NextResponse.next();
  response.headers.set("x-request-id", crypto.randomUUID());
  return response;
}
```

**Footgun**: Heavy operations in middleware slow down EVERY page load. Keep middleware under 5ms.

## See Also

- `hub/deployment-patterns.md` — Runtime selection per route
- `hub/authentication-patterns.md` — Auth on edge
- `hub/database-patterns.md` — Edge-compatible database access
