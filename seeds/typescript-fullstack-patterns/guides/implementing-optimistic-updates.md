---
title: "Implementing Optimistic UI Updates"
tags: [guide, optimistic-updates, useOptimistic, server-actions, ux, react]
content_type: guide
domain: typescript-fullstack
---

# Implementing Optimistic UI Updates

## What Are Optimistic Updates?

Show the result of an action immediately, before the server confirms. If the server fails, revert. This makes your app feel instant.

## Step 1: Identify the Candidate

Good candidates for optimistic updates:
- Like/unlike buttons
- Adding items to a list
- Toggling checkboxes
- Deleting items
- Updating text fields

Bad candidates:
- Payment processing (never fake a payment)
- Actions with complex validation
- Actions that depend on server-side computation

## Step 2: Create the Server Action

```typescript
// app/actions.ts
"use server";

import { revalidatePath } from "next/cache";
import { db } from "@/lib/db";

export async function toggleTodo(id: string, completed: boolean) {
  await db.todo.update({
    where: { id },
    data: { completed },
  });
  revalidatePath("/todos");
}

export async function addTodo(title: string) {
  await db.todo.create({
    data: { title, completed: false },
  });
  revalidatePath("/todos");
}

export async function deleteTodo(id: string) {
  await db.todo.delete({ where: { id } });
  revalidatePath("/todos");
}
```

## Step 3: Use `useOptimistic` for Toggle

```typescript
"use client";

import { useOptimistic } from "react";
import { toggleTodo } from "@/app/actions";

type Todo = { id: string; title: string; completed: boolean };

export function TodoList({ todos }: { todos: Todo[] }) {
  const [optimisticTodos, updateOptimistic] = useOptimistic(
    todos,
    (state, { id, completed }: { id: string; completed: boolean }) =>
      state.map((t) => (t.id === id ? { ...t, completed } : t))
  );

  async function handleToggle(id: string, currentCompleted: boolean) {
    const newCompleted = !currentCompleted;
    updateOptimistic({ id, completed: newCompleted });
    await toggleTodo(id, newCompleted);
    // If toggleTodo throws, React reverts to the original state
  }

  return (
    <ul>
      {optimisticTodos.map((todo) => (
        <li key={todo.id}>
          <input
            type="checkbox"
            checked={todo.completed}
            onChange={() => handleToggle(todo.id, todo.completed)}
          />
          <span className={todo.completed ? "line-through opacity-50" : ""}>
            {todo.title}
          </span>
        </li>
      ))}
    </ul>
  );
}
```

## Step 4: Optimistic Add with Pending State

```typescript
"use client";

import { useOptimistic } from "react";
import { addTodo } from "@/app/actions";

type OptimisticTodo = Todo & { pending?: boolean };

export function TodoListWithAdd({ todos }: { todos: Todo[] }) {
  const [optimisticTodos, addOptimistic] = useOptimistic<OptimisticTodo[], string>(
    todos,
    (state, newTitle) => [
      ...state,
      { id: `temp-${Date.now()}`, title: newTitle, completed: false, pending: true },
    ]
  );

  async function handleAdd(formData: FormData) {
    const title = formData.get("title") as string;
    addOptimistic(title);
    await addTodo(title);
  }

  return (
    <>
      <form action={handleAdd}>
        <input name="title" required placeholder="New todo" />
        <button type="submit">Add</button>
      </form>
      <ul>
        {optimisticTodos.map((todo) => (
          <li key={todo.id} className={todo.pending ? "opacity-50" : ""}>
            {todo.title}
            {todo.pending && <span className="ml-2 text-xs">Saving...</span>}
          </li>
        ))}
      </ul>
    </>
  );
}
```

## Step 5: Optimistic Delete

```typescript
const [optimisticTodos, removeOptimistic] = useOptimistic<Todo[], string>(
  todos,
  (state, deletedId) => state.filter((t) => t.id !== deletedId)
);

async function handleDelete(id: string) {
  removeOptimistic(id);
  await deleteTodo(id);
}
```

## Error Handling

React automatically reverts optimistic state when the server action throws. For custom error handling:

```typescript
async function handleToggle(id: string) {
  updateOptimistic({ id, completed: true });
  try {
    await toggleTodo(id, true);
  } catch {
    toast.error("Failed to update. Please try again.");
    // React already reverted the optimistic state
  }
}
```

## See Also

- `hub/form-handling.md` — Form submission patterns
- `hub/react-server-components.md` — Server action details
- `hub/state-management.md` — Client-side state patterns
