---
title: "Writing for Different Audiences"
tags: [audience, beginner, intermediate, expert, layered-docs, personas, documentation]
content_type: research
domain: technical-writing
---

# Writing for Different Audiences

## The Problem

Beginners want hand-holding. Experts want quick answers. The same document can't serve both without frustrating everyone. The solution isn't to pick one audience -- it's to layer your content.

## Layered Documentation

### Layer 1: Quickstart (Beginners)

Get them to "hello world" as fast as possible. Assume nothing.

```markdown
## Quick Start

1. Install:  brew install mytool
2. Create a project:  mytool init my-project
3. Run it:  cd my-project && mytool dev
4. Open http://localhost:3000 — you should see the welcome page.
```

No theory. No options. One path that works.

### Layer 2: Guides (Intermediate)

They know the basics. They need to do specific things.

```markdown
## Add Authentication

This guide assumes you've completed the quickstart and have a
running project.

1. Install the auth plugin:  mytool plugin add auth
2. Configure your provider in config.toml:
   [auth]
   provider = "oauth2"
   client_id = "your-client-id"
3. Add the auth middleware to your routes...
```

Assumes context. Focused on a task. Links to reference for details.

### Layer 3: Reference (Experts)

Complete, factual, organized for lookup. No hand-holding.

```markdown
## Configuration Reference

### [auth]

| Key | Type | Default | Description |
|-----|------|---------|-------------|
| provider | string | none | Auth provider: "oauth2", "saml", "local" |
| client_id | string | none | OAuth2 client ID |
| session_ttl | duration | "24h" | Session expiration |
| refresh | bool | true | Enable token refresh |
```

Terse. Complete. Organized for Ctrl+F.

## Writing for Each Level

### For Beginners

- Explain every step. Don't skip "obvious" ones.
- Use the simplest possible example.
- Avoid jargon. If you must use a term, define it on first use.
- Show expected output so they know if it worked.
- Link to concepts they might not know.

### For Intermediate Users

- Assume they can install things and read error messages.
- Focus on the task, not the theory behind it.
- Show the common case first, edge cases after.
- Link to reference docs for full option lists.

### For Experts

- Get to the point. No preamble.
- Show the config/API/CLI reference, not a tutorial.
- Include edge cases and gotchas.
- Show performance characteristics if relevant.
- Assume they'll read the source if needed.

## Techniques for Serving Multiple Audiences

### Progressive Disclosure

Show simple content first. Let users expand for details.

```markdown
## Install

brew install mytool

<details>
<summary>Other install methods</summary>

### From source
git clone ... && make install

### Docker
docker pull mytool/mytool:latest

### Windows
Download from https://...
</details>
```

### Audience Labels

Mark content sections with who they're for:

```markdown
> **For beginners:** If you haven't used a CLI tool before,
> see our [terminal basics guide](link) first.
```

### Separate Documents

Sometimes the cleanest approach is separate documents for separate audiences:
- `quickstart.md` -- beginners
- `guides/` -- intermediate
- `reference/` -- experts

This is the Diataxis approach. See `hub/diataxis-framework.md`.

## Common Mistakes

- **Writing only for experts.** Your docs feel hostile to beginners, limiting adoption.
- **Writing only for beginners.** Experts can't find quick answers and stop using docs.
- **Mixing levels in one page.** A tutorial that suddenly drops into API reference confuses everyone.
- **Assuming your level.** You're an expert in your own project. Your readers probably aren't.

## See Also

- `hub/diataxis-framework.md` -- Structural approach to audience segmentation
- `hub/writing-style-guide.md` -- Clarity principles for all audiences
- `hub/onboarding-documentation.md` -- Beginner-focused documentation
- `research/writing-for-international-audiences.md` -- Non-native speakers as an audience
