---
title: "Internationalization (i18n)"
tags: [i18n, internationalization, next-intl, localization, rtl, icu, translation]
content_type: research
domain: typescript-fullstack
---

# Internationalization (i18n)

## The Core Idea

i18n in Next.js App Router means: routing by locale, translating strings, formatting dates/numbers, and handling RTL layouts. `next-intl` is the standard library for App Router. It supports Server Components, type-safe messages, and ICU MessageFormat.

## Setup with next-intl

```typescript
// i18n/config.ts
export const locales = ["en", "de", "ja", "ar"] as const;
export type Locale = (typeof locales)[number];
export const defaultLocale: Locale = "en";

// i18n/request.ts
import { getRequestConfig } from "next-intl/server";

export default getRequestConfig(async ({ requestLocale }) => {
  const locale = await requestLocale;
  return {
    locale,
    messages: (await import(`../messages/${locale}.json`)).default,
  };
});
```

```typescript
// middleware.ts
import createMiddleware from "next-intl/middleware";
import { locales, defaultLocale } from "@/i18n/config";

export default createMiddleware({
  locales,
  defaultLocale,
  localePrefix: "as-needed", // Only show /de, /ja — not /en
});

export const config = {
  matcher: ["/((?!api|_next|.*\\..*).*)"],
};
```

## Message Files

```json
// messages/en.json
{
  "common": {
    "welcome": "Welcome, {name}",
    "itemCount": "{count, plural, =0 {No items} one {# item} other {# items}}",
    "lastSeen": "Last seen {date, date, medium}"
  },
  "dashboard": {
    "title": "Dashboard",
    "revenue": "Revenue: {amount, number, currency}"
  }
}
```

```json
// messages/de.json
{
  "common": {
    "welcome": "Willkommen, {name}",
    "itemCount": "{count, plural, =0 {Keine Elemente} one {# Element} other {# Elemente}}",
    "lastSeen": "Zuletzt gesehen {date, date, medium}"
  }
}
```

## Using Translations

```typescript
// Server Component
import { useTranslations } from "next-intl";

export default function Dashboard() {
  const t = useTranslations("dashboard");

  return (
    <div>
      <h1>{t("title")}</h1>
      <p>{t("revenue", { amount: 12500 })}</p>
    </div>
  );
}

// Client Component
"use client";

import { useTranslations } from "next-intl";

export function WelcomeBanner({ name }: { name: string }) {
  const t = useTranslations("common");
  return <h2>{t("welcome", { name })}</h2>;
}
```

## App Router Structure

```
app/
  [locale]/
    layout.tsx        # Sets locale on <html lang>
    page.tsx          # Home page (translated)
    dashboard/
      page.tsx        # Dashboard (translated)
```

```typescript
// app/[locale]/layout.tsx
import { NextIntlClientProvider } from "next-intl";
import { getMessages } from "next-intl/server";

export default async function LocaleLayout({
  children,
  params,
}: {
  children: React.ReactNode;
  params: Promise<{ locale: string }>;
}) {
  const { locale } = await params;
  const messages = await getMessages();

  return (
    <html lang={locale} dir={locale === "ar" ? "rtl" : "ltr"}>
      <body>
        <NextIntlClientProvider messages={messages}>
          {children}
        </NextIntlClientProvider>
      </body>
    </html>
  );
}
```

## RTL Support

```typescript
// Tailwind CSS — logical properties for automatic RTL
<div className="ms-4">   {/* margin-inline-start (left in LTR, right in RTL) */}
<div className="ps-4">   {/* padding-inline-start */}
<div className="text-start"> {/* text-align: start */}
```

**Footgun**: Use logical properties (`ms-`, `me-`, `ps-`, `pe-`, `start`, `end`) instead of physical (`ml-`, `mr-`, `pl-`, `pr-`, `left`, `right`). Tailwind v3.3+ supports them.

## Common Mistakes

1. **Hardcoded strings in components** — every user-visible string should go through `t()`
2. **Not handling plurals** — ICU MessageFormat handles plurals correctly per language
3. **Forgetting RTL** — use logical CSS properties from the start
4. **Loading all locales at once** — only load the current locale's messages
5. **Not setting `<html lang>`** — screen readers and search engines need the language attribute

## See Also

- `hub/nextjs-app-router.md` — Routing patterns
- `hub/tailwind-architecture.md` — RTL-compatible styling
