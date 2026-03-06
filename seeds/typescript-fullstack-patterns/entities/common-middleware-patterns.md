---
title: "Common Middleware Patterns"
tags: [middleware, nextjs, auth, redirect, headers, rate-limiting, geo, reference, entity]
content_type: entity
domain: typescript-fullstack
---

# Common Middleware Patterns

## How Middleware Works

Middleware runs on the **edge runtime** before every matched request. It can: redirect, rewrite, set headers, and return responses. It cannot: access the database directly (use edge-compatible clients), run Node.js APIs, or modify the response body.

## Auth Protection

```typescript
// middleware.ts
import { auth } from "@/auth";
import { NextResponse } from "next/server";

const publicPaths = ["/", "/login", "/signup", "/api/webhooks"];

export default auth((req) => {
  const isPublic = publicPaths.some((p) => req.nextUrl.pathname.startsWith(p));
  if (!isPublic && !req.auth) {
    return NextResponse.redirect(new URL("/login", req.url));
  }
});

export const config = {
  matcher: ["/((?!_next/static|_next/image|favicon.ico|public/).*)"],
};
```

## Geo-Based Redirect

```typescript
export function middleware(request: NextRequest) {
  const country = request.geo?.country ?? "US";
  const pathname = request.nextUrl.pathname;

  // Redirect EU users to EU-compliant version
  if (["DE", "FR", "IT", "ES"].includes(country) && !pathname.startsWith("/eu")) {
    return NextResponse.redirect(new URL(`/eu${pathname}`, request.url));
  }
}
```

## Adding Headers

```typescript
export function middleware(request: NextRequest) {
  const response = NextResponse.next();
  const requestId = crypto.randomUUID();

  // Add request ID for tracing
  response.headers.set("x-request-id", requestId);

  // Security headers
  response.headers.set("X-Frame-Options", "DENY");
  response.headers.set("X-Content-Type-Options", "nosniff");

  return response;
}
```

## A/B Testing

```typescript
export function middleware(request: NextRequest) {
  const variant = request.cookies.get("ab-variant")?.value;

  if (!variant) {
    const response = NextResponse.next();
    const newVariant = Math.random() < 0.5 ? "a" : "b";
    response.cookies.set("ab-variant", newVariant, { maxAge: 60 * 60 * 24 * 30 });
    return response;
  }

  // Rewrite to variant-specific page
  if (request.nextUrl.pathname === "/pricing") {
    return NextResponse.rewrite(new URL(`/pricing/${variant}`, request.url));
  }
}
```

## Maintenance Mode

```typescript
export function middleware(request: NextRequest) {
  if (process.env.MAINTENANCE_MODE === "true") {
    if (!request.nextUrl.pathname.startsWith("/maintenance")) {
      return NextResponse.rewrite(new URL("/maintenance", request.url));
    }
  }
}
```

## Combining Multiple Middleware

```typescript
export function middleware(request: NextRequest) {
  // 1. Rate limiting check
  // 2. Auth check
  // 3. Geo redirect
  // 4. Add headers

  const response = NextResponse.next();
  response.headers.set("x-request-id", crypto.randomUUID());
  return response;
}
```

## Matcher Config

```typescript
export const config = {
  matcher: [
    // Match all paths except static files and images
    "/((?!_next/static|_next/image|favicon.ico|.*\\.(?:svg|png|jpg|jpeg|gif|webp)$).*)",
  ],
};
```

## See Also

- `hub/authentication-patterns.md` — Auth middleware
- `hub/api-route-patterns.md` — Rate limiting
- `research/edge-runtime-limitations.md` — Edge runtime constraints
