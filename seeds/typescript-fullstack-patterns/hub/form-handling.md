---
title: "Form Handling Patterns"
tags: [forms, react-hook-form, zod, server-actions, validation, optimistic-updates, formdata]
content_type: hub
domain: typescript-fullstack
---

# Form Handling Patterns

## Overview

Forms in fullstack TypeScript have three layers: client validation (instant feedback), server validation (security), and the submission mechanism (server actions, API routes, or client-side fetching). The production pattern: share a Zod schema between client and server, use React Hook Form for UX, and server actions for mutations.

## Shared Schema — Single Source of Truth

```typescript
// lib/schemas/user.ts
import { z } from "zod";

export const createUserSchema = z.object({
  name: z.string().min(2, "Name must be at least 2 characters").max(100),
  email: z.string().email("Invalid email address"),
  role: z.enum(["admin", "editor", "viewer"]).default("viewer"),
});

export type CreateUserInput = z.infer<typeof createUserSchema>;
// { name: string; email: string; role: "admin" | "editor" | "viewer" }
```

## React Hook Form + Zod (Client Side)

```typescript
"use client";

import { useForm } from "react-hook-form";
import { zodResolver } from "@hookform/resolvers/zod";
import { createUserSchema, type CreateUserInput } from "@/lib/schemas/user";
import { createUser } from "@/app/actions";

export function CreateUserForm() {
  const form = useForm<CreateUserInput>({
    resolver: zodResolver(createUserSchema),
    defaultValues: { name: "", email: "", role: "viewer" },
  });

  async function onSubmit(data: CreateUserInput) {
    const result = await createUser(data);
    if (result.error) {
      // Server returned field-level errors — set them on the form
      for (const [field, message] of Object.entries(result.error)) {
        form.setError(field as keyof CreateUserInput, { message });
      }
      return;
    }
    form.reset();
  }

  return (
    <form onSubmit={form.handleSubmit(onSubmit)}>
      <div>
        <input {...form.register("name")} placeholder="Name" />
        {form.formState.errors.name && (
          <p className="text-red-500 text-sm">
            {form.formState.errors.name.message}
          </p>
        )}
      </div>
      <div>
        <input {...form.register("email")} placeholder="Email" />
        {form.formState.errors.email && (
          <p className="text-red-500 text-sm">
            {form.formState.errors.email.message}
          </p>
        )}
      </div>
      <button type="submit" disabled={form.formState.isSubmitting}>
        {form.formState.isSubmitting ? "Creating..." : "Create User"}
      </button>
    </form>
  );
}
```

## Server Actions (Server Side)

```typescript
// app/actions.ts
"use server";

import { createUserSchema } from "@/lib/schemas/user";
import { db } from "@/lib/db";
import { revalidatePath } from "next/cache";

type ActionResult = { success: true } | { error: Record<string, string> };

export async function createUser(input: unknown): Promise<ActionResult> {
  // ALWAYS validate on the server — client validation is for UX only
  const parsed = createUserSchema.safeParse(input);

  if (!parsed.success) {
    const errors: Record<string, string> = {};
    for (const issue of parsed.error.issues) {
      errors[issue.path[0] as string] = issue.message;
    }
    return { error: errors };
  }

  // Check business rules
  const existing = await db.user.findUnique({
    where: { email: parsed.data.email },
  });
  if (existing) {
    return { error: { email: "Email already registered" } };
  }

  await db.user.create({ data: parsed.data });
  revalidatePath("/users");
  return { success: true };
}
```

## Progressive Enhancement

Server actions work without JavaScript. Use `<form action={serverAction}>` with FormData for no-JS support. Parse FormData with `formData.get()` and validate with the same Zod schema.

## Optimistic Updates

Show the result before the server confirms. Roll back on failure.

```typescript
"use client";

import { useOptimistic } from "react";
import { createTodo } from "@/app/actions";

export function TodoList({ todos }: { todos: Todo[] }) {
  const [optimisticTodos, addOptimisticTodo] = useOptimistic(
    todos,
    (state, newTodo: Todo) => [...state, newTodo]
  );

  async function handleCreate(formData: FormData) {
    const title = formData.get("title") as string;

    // Show immediately
    addOptimisticTodo({
      id: crypto.randomUUID(),
      title,
      completed: false,
      pending: true, // Visual indicator
    });

    // Server action — on failure, React reverts the optimistic state
    await createTodo(formData);
  }

  return (
    <>
      <form action={handleCreate}>
        <input name="title" required />
        <button type="submit">Add</button>
      </form>
      <ul>
        {optimisticTodos.map((todo) => (
          <li key={todo.id} className={todo.pending ? "opacity-50" : ""}>
            {todo.title}
          </li>
        ))}
      </ul>
    </>
  );
}
```

## Common Mistakes

1. **Skipping server validation** — client validation is bypassable. ALWAYS validate on the server.
2. **Not handling server errors in the form** — show field-level errors from the server, not just toasts.
3. **Giant forms in one component** — break into sub-forms with `useFormContext` for shared state.
4. **Not disabling submit during submission** — leads to double submissions.
5. **Using `onChange` for everything** — use `onBlur` validation for better UX on complex forms.

## See Also

- `entities/zod-schema-patterns.md` — Zod patterns and transforms
- `hub/error-handling.md` — Error handling in forms and API responses
- `hub/react-server-components.md` — Server actions data flow
- `guides/implementing-optimistic-updates.md` — Detailed optimistic update guide
