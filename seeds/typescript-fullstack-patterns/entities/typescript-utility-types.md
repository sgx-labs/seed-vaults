---
title: "TypeScript Utility Types Reference"
tags: [typescript, utility-types, reference, entity, partial, pick, omit, record]
content_type: entity
pinned: true
domain: typescript-fullstack
---

# TypeScript Utility Types Reference

## Object Manipulation

| Type | What it does | Example |
|------|-------------|---------|
| `Partial<T>` | All properties optional | `Partial<User>` — `{ name?: string; email?: string }` |
| `Required<T>` | All properties required | `Required<Partial<User>>` — undoes Partial |
| `Readonly<T>` | All properties readonly | `Readonly<Config>` — prevents mutation |
| `Pick<T, K>` | Keep only specified keys | `Pick<User, "id" \| "name">` — `{ id: string; name: string }` |
| `Omit<T, K>` | Remove specified keys | `Omit<User, "password">` — everything except password |
| `Record<K, V>` | Object with typed keys/values | `Record<string, number>` — `{ [key: string]: number }` |

## Union Manipulation

| Type | What it does | Example |
|------|-------------|---------|
| `Exclude<T, U>` | Remove types from union | `Exclude<"a" \| "b" \| "c", "a">` — `"b" \| "c"` |
| `Extract<T, U>` | Keep types in union | `Extract<string \| number, string>` — `string` |
| `NonNullable<T>` | Remove null/undefined | `NonNullable<string \| null>` — `string` |

## Function Types

| Type | What it does | Example |
|------|-------------|---------|
| `ReturnType<T>` | Get function return type | `ReturnType<typeof getUser>` |
| `Parameters<T>` | Get function params as tuple | `Parameters<typeof fetch>` |
| `Awaited<T>` | Unwrap Promise | `Awaited<Promise<User>>` — `User` |

## String Manipulation

| Type | What it does | Example |
|------|-------------|---------|
| `Uppercase<T>` | Uppercase string literal | `Uppercase<"hello">` — `"HELLO"` |
| `Lowercase<T>` | Lowercase string literal | `Lowercase<"HELLO">` — `"hello"` |
| `Capitalize<T>` | Capitalize first letter | `Capitalize<"hello">` — `"Hello"` |
| `Uncapitalize<T>` | Lowercase first letter | `Uncapitalize<"Hello">` — `"hello"` |

## Practical Patterns

```typescript
// Create input type from model (omit server-generated fields)
type CreateUserInput = Omit<User, "id" | "createdAt" | "updatedAt">;

// Update type (all fields optional except id)
type UpdateUserInput = Partial<Omit<User, "id">> & Pick<User, "id">;

// API response wrapper
type ApiResponse<T> = {
  data: T;
  pagination?: { page: number; total: number };
};

// Type-safe event handler map
type EventHandlers<T extends Record<string, unknown>> = {
  [K in keyof T as `on${Capitalize<string & K>}`]: (payload: T[K]) => void;
};

// Deep partial (recursively make all properties optional)
type DeepPartial<T> = {
  [K in keyof T]?: T[K] extends object ? DeepPartial<T[K]> : T[K];
};
```

## See Also

- `hub/typescript-strict-patterns.md` — Branded types, const assertions
- `research/typescript-type-gymnastics.md` — Advanced type patterns
