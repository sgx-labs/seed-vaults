---
title: "React Server Components vs Client Components"
tags: [react, server-components, client-components, RSC, data-flow, serialization, boundary]
content_type: hub
domain: typescript-fullstack
---

# React Server Components vs Client Components

## Overview

Server Components (RSC) render on the server and send HTML + a serialized component tree to the client. Client Components render on both server (SSR) and client (hydration). The distinction changes how you architect data flow, where you put interactivity, and how large your JavaScript bundle is.

## The Decision Rule

**Server Component** (default in App Router) when:
- Fetching data (database queries, API calls)
- Accessing backend resources (filesystem, environment variables)
- Rendering static or data-driven content
- No interactivity needed (no onClick, useState, useEffect)

**Client Component** (`"use client"` directive) when:
- Using hooks (useState, useEffect, useContext, useRef)
- Adding event handlers (onClick, onChange, onSubmit)
- Using browser APIs (localStorage, IntersectionObserver, window)
- Using third-party libraries that require client-side rendering

```typescript
// Server Component (default) — fetches data, no JS shipped
// app/users/page.tsx
import { db } from "@/lib/db";

export default async function UsersPage() {
  const users = await db.user.findMany({
    select: { id: true, name: true, email: true },
  });

  return (
    <div>
      <h1>Users</h1>
      <UserList users={users} />
      <AddUserButton /> {/* Client Component for interactivity */}
    </div>
  );
}
```

```typescript
// Client Component — handles interactivity
// components/add-user-button.tsx
"use client";

import { useState } from "react";

export function AddUserButton() {
  const [isOpen, setIsOpen] = useState(false);
  return (
    <>
      <button onClick={() => setIsOpen(true)}>Add User</button>
      {isOpen && <AddUserDialog onClose={() => setIsOpen(false)} />}
    </>
  );
}
```

## The Serialization Boundary

When a Server Component renders a Client Component, props must be serializable. This is the #1 source of RSC bugs.

**Serializable** (safe to pass as props):
- Primitives: string, number, boolean, null, undefined
- Plain objects and arrays (with serializable values)
- Date (serialized as string)
- Server Actions (functions with `"use server"`)

**NOT serializable** (will throw an error):
- Functions (except Server Actions)
- Classes, Maps, Sets
- Symbols
- JSX (React elements) — but see the children pattern below

## The Children Pattern

Pass Server Components as children to Client Components. The server renders the children, and the client component receives pre-rendered JSX.

```typescript
// Client Component wrapper
"use client";

export function Sidebar({ children }: { children: React.ReactNode }) {
  const [isOpen, setIsOpen] = useState(true);
  return (
    <aside className={isOpen ? "w-64" : "w-0"}>
      <button onClick={() => setIsOpen(!isOpen)}>Toggle</button>
      {children} {/* Server-rendered content injected here */}
    </aside>
  );
}

// Server Component parent
import { Sidebar } from "./sidebar";
import { db } from "@/lib/db";

export default async function Layout({ children }: { children: React.ReactNode }) {
  const nav = await db.navItem.findMany(); // Server-side fetch
  return (
    <Sidebar>
      <NavList items={nav} /> {/* Server Component rendered on server */}
    </Sidebar>
  );
}
```

## Data Flow Patterns

### Pattern 1: Fetch at the top, pass down

```typescript
// Server Component fetches, passes data to Client Component
async function Dashboard() {
  const stats = await getStats(); // Server-side
  return <StatsChart data={stats} />; // Client Component receives plain data
}
```

### Pattern 2: Multiple independent fetches with Suspense

```typescript
async function Dashboard() {
  return (
    <>
      <Suspense fallback={<Skeleton />}>
        <RevenueChart /> {/* Each fetches independently */}
      </Suspense>
      <Suspense fallback={<Skeleton />}>
        <UserGrowth />
      </Suspense>
    </>
  );
}
```

### Pattern 3: Server Actions for mutations

```typescript
// Server Action
"use server";

export async function createUser(formData: FormData) {
  const name = formData.get("name") as string;
  await db.user.create({ data: { name } });
  revalidatePath("/users");
}

// Client Component form
"use client";

export function CreateUserForm() {
  return (
    <form action={createUser}>
      <input name="name" required />
      <button type="submit">Create</button>
    </form>
  );
}
```

## Common Mistakes

1. **Adding `"use client"` to everything** — you lose RSC benefits. Only add it where you need interactivity.
2. **Fetching data in Client Components** — move fetches to Server Components when possible.
3. **Passing non-serializable props** — functions, Maps, class instances can't cross the boundary.
4. **Importing server-only code in Client Components** — use the `server-only` package to prevent this.
5. **Not using Suspense boundaries** — without them, the entire page waits for the slowest fetch.

```typescript
// Protect server-only code from client imports
// lib/db.ts
import "server-only";
import { PrismaClient } from "@prisma/client";
export const db = new PrismaClient();
```

## See Also

- `hub/nextjs-app-router.md` — App Router file conventions and routing
- `hub/form-handling.md` — Server Actions for form mutations
- `hub/state-management.md` — When to use server state vs client state
- `hub/performance-patterns.md` — Streaming and Suspense patterns
