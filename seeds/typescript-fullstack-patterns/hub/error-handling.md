---
title: "Error Handling Patterns"
tags: [errors, result-types, error-boundaries, toast, graceful-degradation, try-catch, discriminated-unions]
content_type: hub
domain: typescript-fullstack
---

# Error Handling Patterns

## Overview

Error handling in fullstack TypeScript spans three layers: server-side (database errors, auth failures), API boundary (structured error responses), and client-side (error boundaries, toast notifications). The production pattern: use Result types for predictable errors, error boundaries for unexpected crashes, and toast notifications for user feedback.

## Result Types — No More try/catch Spaghetti

Model success and failure as a discriminated union. Forces callers to handle both cases.

```typescript
// lib/result.ts
type Result<T, E = string> =
  | { ok: true; data: T }
  | { ok: false; error: E };

function ok<T>(data: T): Result<T, never> {
  return { ok: true, data };
}

function err<E>(error: E): Result<never, E> {
  return { ok: false, error };
}

// Usage in server actions
"use server";

type CreateUserError =
  | { code: "DUPLICATE_EMAIL"; email: string }
  | { code: "VALIDATION"; fields: Record<string, string> }
  | { code: "INTERNAL"; message: string };

export async function createUser(
  input: CreateUserInput
): Promise<Result<User, CreateUserError>> {
  const parsed = schema.safeParse(input);
  if (!parsed.success) {
    return err({
      code: "VALIDATION",
      fields: Object.fromEntries(
        parsed.error.issues.map((i) => [i.path[0], i.message])
      ),
    });
  }

  try {
    const user = await db.user.create({ data: parsed.data });
    return ok(user);
  } catch (e) {
    if (e instanceof Prisma.PrismaClientKnownRequestError && e.code === "P2002") {
      return err({ code: "DUPLICATE_EMAIL", email: parsed.data.email });
    }
    return err({ code: "INTERNAL", message: "Failed to create user" });
  }
}

// Caller handles both cases explicitly
const result = await createUser(data);
if (!result.ok) {
  switch (result.error.code) {
    case "DUPLICATE_EMAIL":
      form.setError("email", { message: "Email already taken" });
      break;
    case "VALIDATION":
      // Set field errors
      break;
    case "INTERNAL":
      toast.error("Something went wrong. Please try again.");
      break;
  }
  return;
}
// result.data is narrowed to User here
```

## Error Boundaries — Catch the Unexpected

Error boundaries catch rendering errors. In Next.js App Router, use `error.tsx` files.

```typescript
// app/dashboard/error.tsx — must be "use client"
"use client";

export default function DashboardError({
  error,
  reset,
}: {
  error: Error & { digest?: string };
  reset: () => void;
}) {
  useEffect(() => { reportError(error); }, [error]);

  return (
    <div>
      <h2>Something went wrong</h2>
      <button onClick={reset}>Try again</button>
    </div>
  );
}
```

**Granular boundaries** — wrap risky sections, not entire pages:

```typescript
import { ErrorBoundary } from "react-error-boundary";

export function Dashboard() {
  return (
    <div>
      <h1>Dashboard</h1>
      <ErrorBoundary fallback={<p>Failed to load chart</p>}>
        <RevenueChart /> {/* If this crashes, only the chart shows an error */}
      </ErrorBoundary>
      <UserTable /> {/* Unaffected by chart errors */}
    </div>
  );
}
```

## Toast Notifications

For transient feedback (success, warning, info) that doesn't require user action.

```typescript
import { toast } from "sonner"; // Recommended toast library for Next.js

const result = await deleteItem(id);
if (result.ok) toast.success("Item deleted");
else toast.error("Failed to delete", { description: result.error.message });
```

**Setup**: Add `<Toaster richColors position="bottom-right" />` in your root layout.

## API Route Error Handling

```typescript
// app/api/users/route.ts
import { NextResponse } from "next/server";
import { ZodError } from "zod";

export async function POST(req: Request) {
  try {
    const body = await req.json();
    const data = createUserSchema.parse(body);
    const user = await db.user.create({ data });
    return NextResponse.json(user, { status: 201 });
  } catch (error) {
    if (error instanceof ZodError) {
      return NextResponse.json(
        { error: "Validation failed", details: error.flatten().fieldErrors },
        { status: 422 }
      );
    }
    if (error instanceof Prisma.PrismaClientKnownRequestError) {
      if (error.code === "P2002") {
        return NextResponse.json(
          { error: "Resource already exists" },
          { status: 409 }
        );
      }
    }
    console.error("Unhandled error:", error);
    return NextResponse.json(
      { error: "Internal server error" },
      { status: 500 }
    );
  }
}
```

## Common Mistakes

1. **Swallowing errors with empty catch blocks** — at minimum, log them
2. **Showing raw error messages to users** — expose details in dev, generic messages in prod
3. **Not distinguishing expected vs unexpected errors** — Result types for expected, error boundaries for unexpected
4. **Toasting everything** — use toasts for actions, not for page-level errors
5. **Forgetting `global-error.tsx`** — root layout errors aren't caught by `error.tsx`

## See Also

- `hub/typescript-strict-patterns.md` — Discriminated unions for error modeling
- `hub/form-handling.md` — Form-specific error handling
- `hub/api-route-patterns.md` — API error responses
- `entities/http-status-codes.md` — Choosing the right status code
