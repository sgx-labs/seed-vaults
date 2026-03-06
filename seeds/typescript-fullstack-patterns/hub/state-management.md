---
title: "State Management Patterns"
tags: [state, zustand, react-query, tanstack-query, url-state, server-state, client-state, context]
content_type: hub
domain: typescript-fullstack
---

# State Management Patterns

## Overview

The biggest state management mistake in fullstack apps: treating all state the same. Server state (data from your API) and client state (UI toggles, form inputs) have fundamentally different characteristics and need different tools. With Server Components, a third category emerges: state that never reaches the client at all.

## The Three Buckets

| Bucket | Examples | Tool |
|--------|----------|------|
| **Server state** | User profile, posts list, dashboard data | React Query / SWR / Server Components |
| **Client state** | Theme, sidebar open/closed, modal visibility | Zustand / useState / useReducer |
| **URL state** | Filters, pagination, search, tabs | useSearchParams / nuqs |

## Server State — React Query (TanStack Query)

Server state is data you don't own -- it lives on the server and can become stale. React Query handles fetching, caching, revalidation, and optimistic updates.

```typescript
// hooks/use-users.ts
import { useQuery, useMutation, useQueryClient } from "@tanstack/react-query";

export function useUsers() {
  return useQuery({
    queryKey: ["users"],
    queryFn: () => fetch("/api/users").then((r) => r.json()),
    staleTime: 5 * 60 * 1000, // Consider fresh for 5 minutes
  });
}

export function useCreateUser() {
  const queryClient = useQueryClient();

  return useMutation({
    mutationFn: (data: CreateUserInput) =>
      fetch("/api/users", {
        method: "POST",
        body: JSON.stringify(data),
        headers: { "Content-Type": "application/json" },
      }).then((r) => r.json()),
    onSuccess: () => {
      queryClient.invalidateQueries({ queryKey: ["users"] });
    },
  });
}
```

**With Server Components** — you often don't need React Query at all:

```typescript
// Server Component — no client-side state management needed
export default async function UsersPage() {
  const users = await db.user.findMany();
  return <UserList users={users} />;
}
```

Use React Query in Client Components when you need: polling, optimistic updates, infinite scroll, or client-side refetching.

## Client State — Zustand

For client-only state that multiple components need. Zustand is tiny (1KB), has no boilerplate, and works with Server Components (store lives in Client Components only).

```typescript
// stores/ui-store.ts
import { create } from "zustand";

interface UIState {
  sidebarOpen: boolean;
  toggleSidebar: () => void;
  theme: "light" | "dark" | "system";
  setTheme: (theme: UIState["theme"]) => void;
}

export const useUIStore = create<UIState>((set) => ({
  sidebarOpen: true,
  toggleSidebar: () => set((s) => ({ sidebarOpen: !s.sidebarOpen })),
  theme: "system",
  setTheme: (theme) => set({ theme }),
}));
```

**With persistence:**

```typescript
import { create } from "zustand";
import { persist } from "zustand/middleware";

export const useUIStore = create<UIState>()(
  persist(
    (set) => ({
      sidebarOpen: true,
      toggleSidebar: () => set((s) => ({ sidebarOpen: !s.sidebarOpen })),
      theme: "system",
      setTheme: (theme) => set({ theme }),
    }),
    {
      name: "ui-preferences",
      // Only persist specific fields
      partialize: (state) => ({ theme: state.theme }),
    }
  )
);
```

**Footgun**: Zustand stores are singletons. In Server Components, a shared store would leak data between requests. Only use Zustand in Client Components.

## URL State — The Most Underused Pattern

Filters, search queries, pagination, and tab selection belong in the URL. This gives you: shareable links, browser back/forward, bookmarks, and SSR.

```typescript
// Using nuqs (type-safe URL state)
"use client";

import { useQueryState, parseAsInteger, parseAsStringEnum } from "nuqs";

const sortOptions = ["newest", "oldest", "popular"] as const;

export function UserFilters() {
  const [search, setSearch] = useQueryState("q", { defaultValue: "" });
  const [page, setPage] = useQueryState("page", parseAsInteger.withDefault(1));
  const [sort, setSort] = useQueryState(
    "sort",
    parseAsStringEnum(sortOptions).withDefault("newest")
  );

  return (
    <div>
      <input value={search} onChange={(e) => setSearch(e.target.value)} />
      <select value={sort} onChange={(e) => setSort(e.target.value as any)}>
        {sortOptions.map((s) => <option key={s}>{s}</option>)}
      </select>
    </div>
  );
}
```

## When NOT to Use Each

- **React Query** — Don't use for pure client state (theme, modals). Don't use if Server Components handle your data fetching.
- **Zustand** — Don't use for server state. Don't use for state that only one component needs (just use `useState`).
- **URL state** — Don't use for ephemeral UI state (tooltip visibility). Don't use for sensitive data (passwords, tokens).
- **React Context** — Don't use for frequently changing values (causes re-renders of the entire tree). Good for: themes, auth, i18n.

## Decision Framework

1. Does it come from the server? -> Server Component fetch or React Query
2. Does it need to survive page refresh? -> URL state or persisted Zustand
3. Do multiple components need it? -> Zustand or Context
4. Does only one component need it? -> useState
5. Is it derived from other state? -> useMemo or computed selector

## See Also

- `hub/react-server-components.md` — Server-side data fetching patterns
- `hub/form-handling.md` — Form state management
- `hub/performance-patterns.md` — Avoiding re-render storms
