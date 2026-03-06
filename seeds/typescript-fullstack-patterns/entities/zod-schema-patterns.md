---
title: "Zod Schema Patterns"
tags: [zod, validation, schema, transforms, coercion, reference, entity]
content_type: entity
domain: typescript-fullstack
---

# Zod Schema Patterns

## Basic Schemas

```typescript
import { z } from "zod";

// Primitives
const nameSchema = z.string().min(2).max(100);
const ageSchema = z.number().int().positive().max(150);
const emailSchema = z.string().email();
const urlSchema = z.string().url();
const uuidSchema = z.string().uuid();

// Enums
const roleSchema = z.enum(["admin", "editor", "viewer"]);
type Role = z.infer<typeof roleSchema>; // "admin" | "editor" | "viewer"

// Objects
const userSchema = z.object({
  name: z.string().min(2),
  email: z.string().email(),
  age: z.number().optional(),
  role: roleSchema.default("viewer"),
});
type User = z.infer<typeof userSchema>;
```

## Form Validation Patterns

```typescript
// Passwords with confirmation
const signupSchema = z.object({
  email: z.string().email(),
  password: z.string().min(8).regex(/[A-Z]/, "Must contain uppercase").regex(/[0-9]/, "Must contain number"),
  confirmPassword: z.string(),
}).refine((data) => data.password === data.confirmPassword, {
  message: "Passwords don't match",
  path: ["confirmPassword"],
});

// Date range validation
const dateRangeSchema = z.object({
  startDate: z.coerce.date(),
  endDate: z.coerce.date(),
}).refine((data) => data.endDate > data.startDate, {
  message: "End date must be after start date",
  path: ["endDate"],
});
```

## Transforms

```typescript
// Trim and lowercase
const emailSchema = z.string().email().trim().toLowerCase();

// Parse string to number (from form inputs)
const priceSchema = z.coerce.number().positive();

// Transform on output
const slugSchema = z.string().transform((val) =>
  val.toLowerCase().replace(/\s+/g, "-").replace(/[^a-z0-9-]/g, "")
);

// Parse dates from strings
const dateSchema = z.coerce.date(); // Accepts string, number, Date
```

## Discriminated Unions

```typescript
const paymentSchema = z.discriminatedUnion("method", [
  z.object({ method: z.literal("card"), cardNumber: z.string(), cvc: z.string() }),
  z.object({ method: z.literal("bank"), accountNumber: z.string(), routingNumber: z.string() }),
  z.object({ method: z.literal("crypto"), walletAddress: z.string() }),
]);
```

## API Validation

```typescript
// Paginated list query
const paginationSchema = z.object({
  page: z.coerce.number().int().positive().default(1),
  limit: z.coerce.number().int().min(1).max(100).default(20),
  sort: z.enum(["newest", "oldest", "popular"]).default("newest"),
  search: z.string().optional(),
});

// Validate search params in API route
export async function GET(request: Request) {
  const { searchParams } = new URL(request.url);
  const params = paginationSchema.parse(Object.fromEntries(searchParams));
  // params is fully typed with defaults applied
}
```

## Composing Schemas

```typescript
// Base schema
const baseUserSchema = z.object({
  name: z.string().min(2),
  email: z.string().email(),
});

// Extend for different contexts
const createUserSchema = baseUserSchema.extend({
  password: z.string().min(8),
});

const updateUserSchema = baseUserSchema.partial(); // All fields optional

const adminUserSchema = baseUserSchema.extend({
  role: z.literal("admin"),
  permissions: z.array(z.string()),
});

// Merge two schemas
const withTimestamps = z.object({
  createdAt: z.date(),
  updatedAt: z.date(),
});
const fullUserSchema = baseUserSchema.merge(withTimestamps);
```

## Error Handling

```typescript
const result = schema.safeParse(input);

if (!result.success) {
  // Flat format (good for forms)
  const flat = result.error.flatten();
  // { formErrors: string[], fieldErrors: { name?: string[], email?: string[] } }

  // Formatted (nested)
  const formatted = result.error.format();
  // { name?: { _errors: string[] }, email?: { _errors: string[] } }
}
```

## See Also

- `hub/form-handling.md` — Forms with React Hook Form + Zod
- `hub/api-route-patterns.md` — API validation
- `hub/typescript-strict-patterns.md` — Branded types with Zod
