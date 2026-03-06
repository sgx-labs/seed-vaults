---
title: "API Route Patterns"
tags: [api, routes, route-handlers, middleware, validation, rate-limiting, nextjs, rest]
content_type: hub
domain: typescript-fullstack
---

# API Route Patterns

## Overview

Next.js App Router uses `route.ts` files for API endpoints. They handle HTTP methods as named exports, run on the server, and support both Node.js and edge runtimes. This note covers patterns for building production API routes: validation, auth, rate limiting, and error handling.

## Basic Route Handler

```typescript
// app/api/users/route.ts
import { NextResponse } from "next/server";
import { db } from "@/lib/db";

export async function GET(request: Request) {
  const { searchParams } = new URL(request.url);
  const page = parseInt(searchParams.get("page") ?? "1");
  const limit = Math.min(parseInt(searchParams.get("limit") ?? "20"), 100);

  const users = await db.user.findMany({
    skip: (page - 1) * limit,
    take: limit,
    select: { id: true, name: true, email: true, createdAt: true },
    orderBy: { createdAt: "desc" },
  });

  const total = await db.user.count();

  return NextResponse.json({
    data: users,
    pagination: { page, limit, total, pages: Math.ceil(total / limit) },
  });
}
```

## Dynamic Routes with Params

```typescript
// app/api/users/[id]/route.ts
export async function GET(
  request: Request,
  { params }: { params: Promise<{ id: string }> }
) {
  const { id } = await params;

  const user = await db.user.findUnique({
    where: { id },
    select: { id: true, name: true, email: true },
  });

  if (!user) {
    return NextResponse.json({ error: "User not found" }, { status: 404 });
  }

  return NextResponse.json(user);
}
```

## Validated Route Handler Pattern

Create a reusable wrapper that handles validation, auth, and errors.

```typescript
// lib/api-handler.ts
import { NextResponse } from "next/server";
import { ZodSchema, ZodError } from "zod";
import { auth } from "@/auth";

type HandlerContext<TBody = unknown> = {
  body: TBody;
  userId: string;
  params: Record<string, string>;
  searchParams: URLSearchParams;
};

export function apiHandler<TBody>(options: {
  schema?: ZodSchema<TBody>;
  requireAuth?: boolean;
  handler: (ctx: HandlerContext<TBody>) => Promise<NextResponse>;
}) {
  return async (request: Request, { params }: { params?: Promise<Record<string, string>> } = {}) => {
    try {
      // Auth check
      let userId = "";
      if (options.requireAuth !== false) {
        const session = await auth();
        if (!session?.user?.id) {
          return NextResponse.json({ error: "Unauthorized" }, { status: 401 });
        }
        userId = session.user.id;
      }

      // Parse body
      let body = {} as TBody;
      if (options.schema) {
        const raw = await request.json().catch(() => ({}));
        body = options.schema.parse(raw);
      }

      const resolvedParams = params ? await params : {};
      const searchParams = new URL(request.url).searchParams;

      return await options.handler({ body, userId, params: resolvedParams, searchParams });
    } catch (error) {
      if (error instanceof ZodError) {
        return NextResponse.json(
          { error: "Validation failed", details: error.flatten().fieldErrors },
          { status: 422 }
        );
      }
      console.error("API error:", error);
      return NextResponse.json(
        { error: "Internal server error" },
        { status: 500 }
      );
    }
  };
}

// Usage
// app/api/posts/route.ts
import { apiHandler } from "@/lib/api-handler";
import { createPostSchema } from "@/lib/schemas";

export const POST = apiHandler({
  schema: createPostSchema,
  handler: async ({ body, userId }) => {
    const post = await db.post.create({
      data: { ...body, authorId: userId },
    });
    return NextResponse.json(post, { status: 201 });
  },
});
```

## Rate Limiting

```typescript
// lib/rate-limit.ts
const rateLimitMap = new Map<string, { count: number; resetAt: number }>();

export function rateLimit(key: string, limit: number, windowMs: number): boolean {
  const now = Date.now();
  const entry = rateLimitMap.get(key);
  if (!entry || now > entry.resetAt) {
    rateLimitMap.set(key, { count: 1, resetAt: now + windowMs });
    return true;
  }
  if (entry.count >= limit) return false;
  entry.count++;
  return true;
}

// Usage in route handler
export async function POST(request: Request) {
  const ip = request.headers.get("x-forwarded-for") ?? "unknown";

  if (!rateLimit(ip, 10, 60_000)) {
    return NextResponse.json(
      { error: "Too many requests" },
      { status: 429, headers: { "Retry-After": "60" } }
    );
  }
  // ... handle request
}
```

**Production version**: Use Upstash Redis for distributed rate limiting across serverless functions.

```typescript
import { Ratelimit } from "@upstash/ratelimit";
import { Redis } from "@upstash/redis";

const ratelimit = new Ratelimit({
  redis: Redis.fromEnv(),
  limiter: Ratelimit.slidingWindow(10, "60 s"),
});
```

## Common Mistakes

1. **Not validating input** — every route handler should validate. Never trust `request.json()` directly.
2. **Leaking internal errors** — catch Prisma errors, Zod errors, etc. Return generic messages in production.
3. **Forgetting OPTIONS handler** — CORS preflight requests need an explicit OPTIONS handler.
4. **Not setting Cache-Control** — API responses are not cached by default in App Router but CDN behavior varies.
5. **Mixing API routes and server actions** — use server actions for form mutations, API routes for external consumers.

## See Also

- `hub/error-handling.md` — Error response patterns
- `hub/authentication-patterns.md` — Protecting routes
- `entities/http-status-codes.md` — Status code reference
- `entities/common-middleware-patterns.md` — Next.js middleware
- `research/security-checklist.md` — Security hardening
