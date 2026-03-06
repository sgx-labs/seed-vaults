---
title: "Testing Strategies"
tags: [testing, vitest, playwright, msw, unit-test, integration-test, e2e, test-strategy]
content_type: hub
domain: typescript-fullstack
---

# Testing Strategies

## Overview

Most fullstack apps are either undertested or tested in the wrong places. The production pattern: heavy integration tests at the API/component level, targeted unit tests for complex logic, and a thin layer of E2E tests for critical user flows. This note covers what to test at each layer, which tools to use, and how to avoid test suites that slow you down.

## The Testing Trophy

Not a pyramid -- a trophy. Integration tests give the most confidence per line of test code.

```
    E2E (Playwright)        — 5-10 tests for critical flows
   ________________________
  Integration (Vitest+MSW)  — Bulk of your tests
 __________________________
 Unit (Vitest)              — Complex logic, utils, pure functions
____________________________
 Static (TypeScript+ESLint) — Catches most bugs for free
```

## Unit Tests — Vitest

Test pure logic that doesn't touch the DOM, network, or database.

```typescript
// lib/utils.test.ts
import { describe, it, expect } from "vitest";
import { formatCurrency, slugify, calculateDiscount } from "./utils";

describe("formatCurrency", () => {
  it("formats whole numbers", () => { expect(formatCurrency(1000)).toBe("$1,000.00"); });
  it("handles zero", () => {
    expect(formatCurrency(0)).toBe("$0.00");
  });

  it("rounds to two decimal places", () => {
    expect(formatCurrency(19.999)).toBe("$20.00");
  });
});

describe("calculateDiscount", () => {
  it("applies percentage discount", () => {
    expect(calculateDiscount(100, { type: "percent", value: 20 })).toBe(80);
  });

  it("never goes below zero", () => {
    expect(calculateDiscount(10, { type: "fixed", value: 50 })).toBe(0);
  });
});
```

## Integration Tests — Components with MSW

Test components with their data fetching. MSW intercepts network requests at the service worker level.

```typescript
// __tests__/users-page.test.tsx
import { render, screen, waitFor } from "@testing-library/react";
import userEvent from "@testing-library/user-event";
import { http, HttpResponse } from "msw";
import { setupServer } from "msw/node";
import { UsersPage } from "@/app/users/page";

const server = setupServer(
  http.get("/api/users", () => {
    return HttpResponse.json({
      data: [
        { id: "1", name: "Alice", email: "alice@test.com" },
        { id: "2", name: "Bob", email: "bob@test.com" },
      ],
    });
  })
);

beforeAll(() => server.listen());
afterEach(() => server.resetHandlers());
afterAll(() => server.close());

it("renders users from API", async () => {
  render(<UsersPage />);
  await waitFor(() => {
    expect(screen.getByText("Alice")).toBeInTheDocument();
    expect(screen.getByText("Bob")).toBeInTheDocument();
  });
});

it("shows error state on API failure", async () => {
  server.use(http.get("/api/users", () => HttpResponse.json({}, { status: 500 })));
  render(<UsersPage />);
  await waitFor(() => {
    expect(screen.getByText(/something went wrong/i)).toBeInTheDocument();
  });
});
```

## Server Action Testing

```typescript
// __tests__/actions.test.ts
import { describe, it, expect, vi } from "vitest";
import { createUser } from "@/app/actions";

// Mock the database
vi.mock("@/lib/db", () => ({
  db: {
    user: {
      create: vi.fn().mockResolvedValue({ id: "1", name: "Alice" }),
      findUnique: vi.fn().mockResolvedValue(null),
    },
  },
}));

describe("createUser", () => {
  it("creates user with valid input", async () => {
    const result = await createUser({ name: "Alice", email: "alice@test.com" });
    expect(result).toEqual({ ok: true, data: expect.objectContaining({ name: "Alice" }) });
  });

  it("returns validation error for invalid email", async () => {
    const result = await createUser({ name: "Alice", email: "not-an-email" });
    expect(result.ok).toBe(false);
  });
});
```

## E2E Tests — Playwright

Test critical user flows end-to-end. Keep these minimal -- they're slow and brittle.

```typescript
// e2e/auth-flow.spec.ts
import { test, expect } from "@playwright/test";

test.describe("Authentication", () => {
  test("user can sign up, log in, and access dashboard", async ({ page }) => {
    // Sign up
    await page.goto("/signup");
    await page.fill('[name="email"]', "test@example.com");
    await page.fill('[name="password"]', "SecurePass123!");
    await page.click('button[type="submit"]');

    // Should redirect to dashboard
    await expect(page).toHaveURL("/dashboard");
    await expect(page.getByText("Welcome")).toBeVisible();
  });

  test("unauthenticated user is redirected to login", async ({ page }) => {
    await page.goto("/dashboard");
    await expect(page).toHaveURL(/\/login/);
  });
});
```

**Tip**: Use auth fixtures to avoid logging in on every test -- set cookies/tokens directly in the test setup.

## What NOT to Test

- **Framework behavior** — don't test that `useState` works
- **Third-party libraries** — don't test that Prisma writes to the database
- **Implementation details** — test behavior, not internal state
- **Trivial components** — a component that just renders props doesn't need a test
- **CSS/styling** — use visual regression tools (Chromatic, Percy) if needed

## Vitest Config

```typescript
// vitest.config.ts
import { defineConfig } from "vitest/config";
import react from "@vitejs/plugin-react";
import tsconfigPaths from "vite-tsconfig-paths";

export default defineConfig({
  plugins: [react(), tsconfigPaths()],
  test: {
    environment: "jsdom",
    globals: true,
    setupFiles: ["./tests/setup.ts"],
    coverage: {
      reporter: ["text", "html"],
      exclude: ["**/*.test.ts", "**/*.spec.ts", "**/types/**"],
    },
  },
});
```

## See Also

- `hub/form-handling.md` — Testing forms
- `hub/api-route-patterns.md` — Testing API routes
- `guides/setting-up-ci-cd.md` — Running tests in CI
- `research/analytics-observability.md` — Monitoring in production
