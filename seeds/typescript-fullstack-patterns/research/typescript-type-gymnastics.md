---
title: "TypeScript Type Gymnastics"
tags: [typescript, conditional-types, mapped-types, template-literals, infer, type-level-programming]
content_type: research
domain: typescript-fullstack
---

# TypeScript Type Gymnastics

## The Core Idea

TypeScript's type system is Turing-complete. You can build complex type transformations that catch bugs at compile time with zero runtime cost. This note covers the patterns you'll actually use in production: conditional types, mapped types, template literal types, and the `infer` keyword.

## Conditional Types

```typescript
// Basic conditional
type IsString<T> = T extends string ? true : false;

// Extract only string keys from an object
type StringKeysOf<T> = {
  [K in keyof T]: T[K] extends string ? K : never;
}[keyof T];

type User = { id: number; name: string; email: string; age: number };
type StringKeys = StringKeysOf<User>; // "name" | "email"

// Exclude null/undefined from a type
type NonNullableDeep<T> = T extends null | undefined
  ? never
  : T extends object
  ? { [K in keyof T]: NonNullableDeep<T[K]> }
  : T;
```

## The `infer` Keyword

Extract types from inside other types.

```typescript
// Extract return type of an async function
type UnwrapPromise<T> = T extends Promise<infer U> ? U : T;
type Result = UnwrapPromise<Promise<string>>; // string

// Extract props from a React component
type PropsOf<T> = T extends React.ComponentType<infer P> ? P : never;
type ButtonProps = PropsOf<typeof Button>;

// Extract array element type
type ElementOf<T> = T extends (infer E)[] ? E : never;
type Item = ElementOf<string[]>; // string

// Extract function parameters
type FirstParam<T> = T extends (first: infer F, ...rest: any[]) => any ? F : never;
```

## Mapped Types

Transform object types by iterating over keys.

```typescript
// Make all properties optional (built-in: Partial<T>)
type Optional<T> = { [K in keyof T]?: T[K] };

// Make specific properties required
type RequireKeys<T, K extends keyof T> = T & Required<Pick<T, K>>;

type UserInput = {
  name?: string;
  email?: string;
  role?: string;
};
type CreateUser = RequireKeys<UserInput, "name" | "email">;
// { name: string; email: string; role?: string }

// Prefix all keys
type Prefixed<T, P extends string> = {
  [K in keyof T as `${P}${Capitalize<string & K>}`]: T[K];
};
type PrefixedUser = Prefixed<{ name: string; age: number }, "user">;
// { userName: string; userAge: number }

// Create a type-safe event map
type EventMap<T> = {
  [K in keyof T as `on${Capitalize<string & K>}`]: (payload: T[K]) => void;
};
type Events = EventMap<{ click: MouseEvent; submit: FormData }>;
// { onClick: (payload: MouseEvent) => void; onSubmit: (payload: FormData) => void }
```

## Template Literal Types

Build string patterns at the type level.

```typescript
// API route type safety
type ApiRoute = `/api/${string}`;
type CrudAction = "list" | "get" | "create" | "update" | "delete";
type ResourceRoute<R extends string> = `/api/${R}/${CrudAction}`;

// Type-safe CSS class generation
type Size = "sm" | "md" | "lg" | "xl";
type Spacing = `p-${Size}` | `m-${Size}` | `px-${Size}` | `py-${Size}`;

// Extract path parameters
type ExtractPathParams<T extends string> =
  T extends `${string}[${infer Param}]${infer Rest}`
    ? Param | ExtractPathParams<Rest>
    : never;

type Params = ExtractPathParams<"/users/[userId]/posts/[postId]">;
// "userId" | "postId"

// Build typed fetch functions
type TypedRoutes = {
  "/api/users": { GET: User[]; POST: User };
  "/api/users/[id]": { GET: User; PATCH: User; DELETE: void };
};
```

## Practical Patterns

### Type-safe form field names

```typescript
type FieldPath<T, Prefix extends string = ""> = {
  [K in keyof T]: T[K] extends object
    ? FieldPath<T[K], `${Prefix}${string & K}.`>
    : `${Prefix}${string & K}`;
}[keyof T];

type UserForm = {
  name: string;
  address: {
    street: string;
    city: string;
  };
};

type Fields = FieldPath<UserForm>;
// "name" | "address.street" | "address.city"
```

### Builder pattern with type accumulation

```typescript
class QueryBuilder<T extends Record<string, unknown> = {}> {
  private selections: string[] = [];

  select<K extends string>(field: K): QueryBuilder<T & Record<K, unknown>> {
    this.selections.push(field);
    return this as any;
  }

  build(): T {
    // ... execute query
    return {} as T;
  }
}

// Type narrows with each call
const result = new QueryBuilder()
  .select("name")
  .select("email")
  .build();
// Type: { name: unknown; email: unknown }
```

## When NOT to Use Type Gymnastics

- If a simpler type works, use it. Clever types are hard to debug.
- If your team can't read it, it's too complex. Add a comment or simplify.
- Don't compute at the type level what you can validate at runtime with Zod.

## See Also

- `hub/typescript-strict-patterns.md` — Foundation patterns
- `entities/typescript-utility-types.md` — Built-in utility types
- `entities/zod-schema-patterns.md` — Runtime validation alternative
