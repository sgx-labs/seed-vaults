---
title: "Email Sending Patterns"
tags: [email, resend, react-email, transactional, marketing, templates, smtp]
content_type: research
domain: typescript-fullstack
---

# Email Sending Patterns

## The Core Idea

Transactional email (password resets, receipts, notifications) requires reliability and deliverability. Marketing email (newsletters, campaigns) requires unsubscribe management and analytics. Use Resend + React Email for transactional; Resend or a dedicated service for marketing.

## Resend + React Email — The Modern Stack

Write email templates as React components. Render to HTML. Send via Resend API.

```typescript
// emails/welcome.tsx
import { Html, Head, Body, Container, Text, Button, Heading } from "@react-email/components";

interface WelcomeEmailProps {
  name: string;
  loginUrl: string;
}

export function WelcomeEmail({ name, loginUrl }: WelcomeEmailProps) {
  return (
    <Html>
      <Head />
      <Body style={{ fontFamily: "Arial, sans-serif", backgroundColor: "#f4f4f5" }}>
        <Container style={{ maxWidth: "600px", margin: "0 auto", padding: "20px" }}>
          <Heading>Welcome, {name}</Heading>
          <Text>Thanks for signing up. Click below to get started.</Text>
          <Button
            href={loginUrl}
            style={{
              backgroundColor: "#2563eb",
              color: "#fff",
              padding: "12px 24px",
              borderRadius: "6px",
              textDecoration: "none",
            }}
          >
            Go to Dashboard
          </Button>
        </Container>
      </Body>
    </Html>
  );
}
```

```typescript
// lib/email.ts
import { Resend } from "resend";
import { WelcomeEmail } from "@/emails/welcome";

const resend = new Resend(process.env.RESEND_API_KEY);

export async function sendWelcomeEmail(user: { name: string; email: string }) {
  const { error } = await resend.emails.send({
    from: "App Name <hello@yourapp.com>",
    to: user.email,
    subject: `Welcome to App Name, ${user.name}`,
    react: WelcomeEmail({
      name: user.name,
      loginUrl: `${process.env.NEXT_PUBLIC_APP_URL}/login`,
    }),
  });

  if (error) {
    console.error("Failed to send welcome email:", error);
    throw new Error("Email delivery failed");
  }
}
```

## Preview Emails in Development

```json
// package.json
{
  "scripts": {
    "email:dev": "email dev --dir emails"
  }
}
```

Run `pnpm email:dev` to open a browser preview of all email templates with hot reload.

## Email in Server Actions

```typescript
"use server";

import { sendWelcomeEmail } from "@/lib/email";

export async function signUp(input: SignUpInput) {
  const user = await db.user.create({ data: input });

  // Don't await email — it shouldn't block the response
  sendWelcomeEmail(user).catch((err) => {
    console.error("Background email failed:", err);
  });

  return { success: true };
}
```

**Footgun**: On serverless, background work after the response may be killed. For reliable delivery, use a queue (Inngest, Trigger.dev) to handle email sending.

## Reliable Email with Background Jobs

```typescript
// Using Inngest for reliable email delivery
import { inngest } from "@/lib/inngest";

export const sendWelcome = inngest.createFunction(
  { id: "send-welcome-email", retries: 3 },
  { event: "user/signed-up" },
  async ({ event }) => {
    await sendWelcomeEmail({
      name: event.data.name,
      email: event.data.email,
    });
  }
);

// Trigger from server action
export async function signUp(input: SignUpInput) {
  const user = await db.user.create({ data: input });
  await inngest.send({ name: "user/signed-up", data: { name: user.name, email: user.email } });
  return { success: true };
}
```

## Common Mistakes

1. **Sending email synchronously** — blocks the response. Fire and forget or use a queue.
2. **No error handling** — email services fail. Log errors, retry, or queue for later.
3. **Hardcoded HTML strings** — use React Email for maintainable, type-safe templates.
4. **Sending from a no-reply address** — damages deliverability. Use a real address.
5. **Not setting up SPF/DKIM/DMARC** — without DNS records, emails go to spam.

## See Also

- `research/background-jobs.md` — Reliable job execution
- `hub/authentication-patterns.md` — Password reset emails
