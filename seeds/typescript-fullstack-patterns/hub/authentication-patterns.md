---
title: "Authentication Patterns"
tags: [authentication, auth, nextauth, clerk, lucia, auth-js, session, jwt, middleware, oauth]
content_type: hub
domain: typescript-fullstack
---

# Authentication Patterns

## Overview

Authentication in a fullstack Next.js app touches every layer: middleware, server components, API routes, client components, and database. The right choice depends on your budget, time, and customization needs. This note covers the three main approaches and when each fits.

## NextAuth / Auth.js — The Open-Source Standard

Self-hosted, free, supports dozens of providers. v5 (Auth.js) adds edge runtime support and simpler configuration.

```typescript
// auth.ts (v5 / Auth.js)
import NextAuth from "next-auth";
import GitHub from "next-auth/providers/github";
import Google from "next-auth/providers/google";
import { PrismaAdapter } from "@auth/prisma-adapter";
import { db } from "@/lib/db";

export const { handlers, auth, signIn, signOut } = NextAuth({
  adapter: PrismaAdapter(db),
  providers: [GitHub, Google],
  callbacks: {
    session({ session, user }) {
      session.user.id = user.id;
      session.user.role = user.role;
      return session;
    },
  },
});

// app/api/auth/[...nextauth]/route.ts
export { handlers as GET, handlers as POST };
```

**Middleware protection:**

```typescript
// middleware.ts
import { auth } from "@/auth";

export default auth((req) => {
  const isProtected = req.nextUrl.pathname.startsWith("/dashboard");
  if (isProtected && !req.auth) {
    return Response.redirect(new URL("/login", req.url));
  }
});

export const config = {
  matcher: ["/dashboard/:path*", "/settings/:path*"],
};
```

**Reading session in Server Components:**

```typescript
import { auth } from "@/auth";

export default async function DashboardPage() {
  const session = await auth();
  if (!session) redirect("/login");

  return <h1>Welcome, {session.user.name}</h1>;
}
```

**When to use**: You want full control, open-source, database-backed sessions, and are comfortable managing auth infrastructure.

**When NOT to use**: You need auth in under an hour with no ongoing maintenance, or you need advanced features (MFA, org management) out of the box.

## Clerk — The Managed Solution

Hosted auth service. Drop-in components, user management dashboard, organization support, MFA. Paid beyond free tier.

```typescript
// middleware.ts
import { clerkMiddleware, createRouteMatcher } from "@clerk/nextjs/server";

const isProtectedRoute = createRouteMatcher([
  "/dashboard(.*)",
  "/settings(.*)",
]);

export default clerkMiddleware(async (auth, req) => {
  if (isProtectedRoute(req)) await auth.protect();
});

// In Server Components
import { currentUser } from "@clerk/nextjs/server";

export default async function DashboardPage() {
  const user = await currentUser();
  return <h1>Welcome, {user?.firstName}</h1>;
}
```

**When to use**: You want auth done in minutes, need user management UI, organizations/teams, MFA, and are OK paying for managed infrastructure.

**When NOT to use**: Budget-constrained projects, need full data ownership, or you need deep customization of the auth flow.

## Lucia — The Lightweight Library

Minimal session library. You own everything -- database schema, password hashing, OAuth flows. Lucia handles session management only.

```typescript
// lib/auth.ts
import { Lucia } from "lucia";
import { PrismaAdapter } from "@lucia-auth/adapter-prisma";
import { db } from "@/lib/db";

const adapter = new PrismaAdapter(db.session, db.user);

export const lucia = new Lucia(adapter, {
  sessionCookie: {
    expires: false,
    attributes: { secure: process.env.NODE_ENV === "production" },
  },
  getUserAttributes: (attributes) => ({
    email: attributes.email,
    name: attributes.name,
  }),
});
```

**When to use**: You want maximum control over every auth detail, are building a custom auth flow, or need to support unconventional auth mechanisms.

**When NOT to use**: You want fast setup, need social OAuth with minimal code, or don't want to implement password reset/email verification yourself.

## Comparison

| Factor | NextAuth/Auth.js | Clerk | Lucia |
|--------|-----------------|-------|-------|
| Setup time | 30 min | 10 min | 2+ hours |
| Cost | Free | Free tier, then paid | Free |
| Data ownership | Full | Hosted by Clerk | Full |
| Social OAuth | Built-in | Built-in | DIY |
| MFA | DIY | Built-in | DIY |
| Edge runtime | v5 supports edge | Yes | Yes |
| User management UI | None (DIY) | Full dashboard | None (DIY) |
| Customization | High | Medium | Maximum |
| Organization/teams | DIY | Built-in | DIY |

## Session Patterns

### Type-safe session helper

```typescript
// lib/auth-utils.ts
import { auth } from "@/auth";
import { redirect } from "next/navigation";

export async function requireAuth() {
  const session = await auth();
  if (!session?.user) redirect("/login");
  return session.user; // Type-narrowed: user is guaranteed
}

export async function requireRole(role: "admin" | "editor") {
  const user = await requireAuth();
  if (user.role !== role) redirect("/unauthorized");
  return user;
}

// Usage in Server Components
export default async function AdminPage() {
  const user = await requireRole("admin"); // Redirects if not admin
  return <AdminDashboard userId={user.id} />;
}
```

## Common Mistakes

1. **Checking auth only on the client** — always verify on the server. Client checks are for UX, not security.
2. **No middleware protection** — relying on per-page auth checks means one missed page = security hole.
3. **Storing sensitive data in JWT** — JWTs are encoded, not encrypted. Anyone can read the payload.
4. **Not handling session refresh** — long sessions without refresh lead to either UX or security problems.

## See Also

- `research/security-checklist.md` — XSS, CSRF, headers, CSP
- `hub/api-route-patterns.md` — Protecting API routes
- `guides/adding-auth-to-existing-app.md` — Step-by-step auth integration
- `entities/common-middleware-patterns.md` — Middleware patterns
