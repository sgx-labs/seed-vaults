---
title: "TypeScript Strict Mode Patterns"
tags: [typescript, strict, branded-types, discriminated-unions, const-assertions, type-narrowing, type-safety]
content_type: hub
domain: typescript-fullstack
---

# TypeScript Strict Mode Patterns

## Overview

TypeScript's type system is only as strong as your configuration. Strict mode (`"strict": true` in tsconfig) enables a suite of checks that catch real bugs at compile time. This note covers patterns that leverage strict mode for maximum safety: const assertions, branded types, discriminated unions, and exhaustive type narrowing.

## Const Assertions

`as const` narrows literals to their exact value. Essential for configuration objects and union derivation.

```typescript
// Without as const — types are widened
const ROLES = ["admin", "editor", "viewer"]; // string[]

// With as const — exact literal types
const ROLES = ["admin", "editor", "viewer"] as const; // readonly ["admin", "editor", "viewer"]
type Role = (typeof ROLES)[number]; // "admin" | "editor" | "viewer"

// Configuration objects
const ROUTES = {
  home: "/",
  dashboard: "/dashboard",
  settings: "/settings",
} as const;

type Route = (typeof ROUTES)[keyof typeof ROUTES]; // "/" | "/dashboard" | "/settings"
```

**Footgun**: `as const` makes everything `readonly`. If you pass a const array to a function expecting `string[]`, you'll get a type error. Use `readonly string[]` in function signatures.

## Branded Types

Prevent mixing structurally identical types. A `UserId` and a `PostId` are both strings, but they shouldn't be interchangeable.

```typescript
type Brand<T, B extends string> = T & { readonly __brand: B };

type UserId = Brand<string, "UserId">;
type PostId = Brand<string, "PostId">;
type Email = Brand<string, "Email">;

// Constructor functions with validation
function UserId(id: string): UserId {
  if (!id.startsWith("usr_")) throw new Error(`Invalid user ID: ${id}`);
  return id as UserId;
}

function Email(value: string): Email {
  if (!value.includes("@")) throw new Error(`Invalid email: ${value}`);
  return value as Email;
}

// Now the compiler catches this mistake
function getUser(id: UserId): Promise<User> { /* ... */ }
function getPost(id: PostId): Promise<Post> { /* ... */ }

const userId = UserId("usr_abc123");
const postId = PostId("post_xyz789");

getUser(postId); // Type error — PostId is not assignable to UserId
```

**Production version** — combine with Zod for runtime validation:

```typescript
import { z } from "zod";

const UserIdSchema = z.string().startsWith("usr_").brand<"UserId">();
type UserId = z.infer<typeof UserIdSchema>;

const parsed = UserIdSchema.parse("usr_abc"); // UserId
const invalid = UserIdSchema.parse("abc");    // throws ZodError
```

## Discriminated Unions

Model state machines and domain events. The discriminant field (`type`, `status`, `kind`) lets TypeScript narrow the type automatically.

```typescript
// API response modeling
type ApiResponse<T> =
  | { status: "idle" }
  | { status: "loading" }
  | { status: "success"; data: T }
  | { status: "error"; error: string; retryable: boolean };

function renderResult<T>(response: ApiResponse<T>) {
  switch (response.status) {
    case "idle":
      return null;
    case "loading":
      return <Spinner />;
    case "success":
      return <DataView data={response.data} />; // data is narrowed to T
    case "error":
      return <ErrorView message={response.error} canRetry={response.retryable} />;
  }
}

// Domain events
type OrderEvent =
  | { type: "placed"; orderId: string; items: Item[] }
  | { type: "paid"; orderId: string; paymentId: string }
  | { type: "shipped"; orderId: string; trackingNumber: string }
  | { type: "delivered"; orderId: string; deliveredAt: Date }
  | { type: "cancelled"; orderId: string; reason: string };
```

## Exhaustive Checks

Ensure every union variant is handled. If someone adds a new variant, the compiler catches unhandled cases.

```typescript
function assertNever(value: never): never {
  throw new Error(`Unexpected value: ${JSON.stringify(value)}`);
}

function handleEvent(event: OrderEvent): void {
  switch (event.type) {
    case "placed": /* ... */ break;
    case "paid": /* ... */ break;
    case "shipped": /* ... */ break;
    case "delivered": /* ... */ break;
    case "cancelled": /* ... */ break;
    default:
      assertNever(event); // Compile error if a case is missing
  }
}
```

## Template Literal Types

Build type-safe string patterns without runtime overhead.

```typescript
type HttpMethod = "GET" | "POST" | "PUT" | "PATCH" | "DELETE";
type ApiVersion = "v1" | "v2";
type ApiEndpoint = `/${ApiVersion}/${string}`;

// Route params extraction
type ExtractParams<T extends string> =
  T extends `${infer _}:${infer Param}/${infer Rest}`
    ? Param | ExtractParams<`/${Rest}`>
    : T extends `${infer _}:${infer Param}`
    ? Param
    : never;

type Params = ExtractParams<"/users/:userId/posts/:postId">;
// "userId" | "postId"
```

## Satisfies Operator

Validate a value matches a type without widening it. Added in TypeScript 4.9.

```typescript
type Config = Record<string, { url: string; timeout: number }>;

// Using `satisfies` — validates AND preserves literal types
const config = {
  api: { url: "https://api.example.com", timeout: 5000 },
  auth: { url: "https://auth.example.com", timeout: 3000 },
} satisfies Config;

config.api.url; // type is "https://api.example.com", not string
config.api.timeout; // type is 5000, not number

// vs type annotation — widens
const config2: Config = { /* same */ };
config2.api.url; // type is string — lost the literal
```

## Common Mistakes

1. **Using `any` to silence errors** — use `unknown` and narrow instead
2. **Forgetting `exactOptionalPropertyTypes`** — without it, `undefined` is assignable to optional properties even when you don't want it
3. **Not enabling `noUncheckedIndexedAccess`** — array/object index access returns `T` instead of `T | undefined`
4. **Type assertions (`as`) instead of narrowing** — assertions bypass the type checker; narrowing proves correctness

## Research Notes

- `research/typescript-type-gymnastics.md` — Conditional types, mapped types, template literals deep dive

## See Also

- `entities/typescript-utility-types.md` — Built-in utility types reference
- `hub/error-handling.md` — Result types using discriminated unions
- `hub/form-handling.md` — Zod for runtime validation of branded types
- `entities/zod-schema-patterns.md` — Zod schema patterns
