---
title: "Adding Authentication to an Existing App"
tags: [guide, authentication, nextauth, auth-js, step-by-step]
content_type: guide
domain: typescript-fullstack
---

# Adding Authentication to an Existing App

## Prerequisites

- Next.js App Router project
- Database configured (Prisma or Drizzle)

## Step 1: Install Dependencies

```bash
pnpm add next-auth@beta @auth/prisma-adapter
```

## Step 2: Generate Auth Secret

```bash
npx auth secret
# Adds AUTH_SECRET to .env.local
```

## Step 3: Create Auth Config

```typescript
// src/auth.ts
import NextAuth from "next-auth";
import GitHub from "next-auth/providers/github";
import Google from "next-auth/providers/google";
import { PrismaAdapter } from "@auth/prisma-adapter";
import { db } from "@/lib/db";

export const { handlers, auth, signIn, signOut } = NextAuth({
  adapter: PrismaAdapter(db),
  providers: [GitHub, Google],
  pages: {
    signIn: "/login",    // Custom login page
  },
  callbacks: {
    session({ session, user }) {
      session.user.id = user.id;
      return session;
    },
  },
});
```

## Step 4: Add Auth Database Tables

```bash
# Add to your Prisma schema:
# Account, Session, User, VerificationToken models
# See: https://authjs.dev/reference/adapter/prisma

npx prisma migrate dev --name add-auth
```

## Step 5: Create API Route

```typescript
// src/app/api/auth/[...nextauth]/route.ts
export { handlers as GET, handlers as POST } from "@/auth";
```

## Step 6: Add Middleware

```typescript
// src/middleware.ts
import { auth } from "@/auth";

export default auth((req) => {
  const isProtected = req.nextUrl.pathname.startsWith("/dashboard");
  if (isProtected && !req.auth) {
    return Response.redirect(new URL("/login", req.url));
  }
});

export const config = {
  matcher: ["/((?!api|_next/static|_next/image|favicon.ico).*)"],
};
```

## Step 7: Create Login Page

```typescript
// src/app/login/page.tsx
import { signIn } from "@/auth";

export default function LoginPage() {
  return (
    <div className="flex min-h-screen items-center justify-center">
      <div className="space-y-4">
        <h1 className="text-2xl font-bold">Sign In</h1>
        <form action={async () => { "use server"; await signIn("github"); }}>
          <button type="submit">Sign in with GitHub</button>
        </form>
        <form action={async () => { "use server"; await signIn("google"); }}>
          <button type="submit">Sign in with Google</button>
        </form>
      </div>
    </div>
  );
}
```

## Step 8: Use Session in Components

```typescript
// Server Component
import { auth } from "@/auth";

export default async function DashboardPage() {
  const session = await auth();
  if (!session) redirect("/login");
  return <h1>Welcome, {session.user.name}</h1>;
}

// Client Component
"use client";
import { useSession } from "next-auth/react";

export function UserMenu() {
  const { data: session } = useSession();
  if (!session) return <Link href="/login">Sign In</Link>;
  return <p>{session.user.name}</p>;
}
```

## Step 9: Add Session Provider

```typescript
// src/app/layout.tsx
import { SessionProvider } from "next-auth/react";

export default function RootLayout({ children }: { children: React.ReactNode }) {
  return (
    <html>
      <body>
        <SessionProvider>{children}</SessionProvider>
      </body>
    </html>
  );
}
```

## Step 10: Environment Variables

```bash
# .env.local
AUTH_SECRET="your-secret"
AUTH_GITHUB_ID="your-github-client-id"
AUTH_GITHUB_SECRET="your-github-client-secret"
AUTH_GOOGLE_ID="your-google-client-id"
AUTH_GOOGLE_SECRET="your-google-client-secret"
```

## See Also

- `hub/authentication-patterns.md` — Auth pattern comparison
- `entities/common-middleware-patterns.md` — Middleware patterns
- `research/security-checklist.md` — Security hardening
