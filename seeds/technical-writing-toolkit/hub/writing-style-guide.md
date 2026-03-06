---
title: "Writing Style Guide for Engineers"
tags: [style-guide, writing, clarity, active-voice, concise, scannable]
content_type: hub
domain: technical-writing
---

# Writing Style Guide for Engineers

## The Core Principles

1. **Be concise.** Say it in fewer words.
2. **Be specific.** Vague writing wastes the reader's time.
3. **Be scannable.** Most readers skim. Structure for skimming.

That's it. Everything below is an application of these three principles.

## Concise

**Don't write this:**

> In order to utilize the functionality of the authentication system, it is necessary to first obtain an API key by navigating to the settings page of your account dashboard.

**Write this instead:**

> Get an API key from Settings > API Keys.

Cut filler words: "in order to", "it is necessary to", "utilize" (just say "use"), "functionality" (just say "feature"), "navigate to" (just say "go to").

### The Filler Word Hit List

| Instead of | Write |
|-----------|-------|
| in order to | to |
| utilize | use |
| functionality | feature (or just name it) |
| implement | build, add, create |
| leverage | use |
| it should be noted that | (delete entirely) |
| at this point in time | now |
| in the event that | if |
| a large number of | many |
| due to the fact that | because |

## Specific

**Don't write this:**

> The system may experience degraded performance under heavy load.

**Write this instead:**

> Response times exceed 2 seconds when the server handles more than 500 requests per second.

Numbers, examples, and concrete details beat vague qualifiers every time.

### Vague vs Specific

| Vague | Specific |
|-------|----------|
| "The API is fast" | "P99 latency is under 50ms" |
| "Large files may cause issues" | "Files over 100MB will timeout" |
| "Recently updated" | "Updated 2025-03-01" |
| "Various configurations" | "Supports JSON, YAML, and TOML config files" |
| "Contact the team" | "Message #platform-team in Slack" |

## Scannable

Readers don't read -- they scan. Help them:

- **Use headers.** Break content into sections. A reader should understand the page structure from headers alone.
- **Lead with the conclusion.** Don't build up to the answer. State it first, then explain.
- **Use lists.** Bullets for unordered items. Numbers for sequential steps.
- **Use tables.** For comparisons and reference data.
- **Use code blocks.** For anything that might be copied.
- **Bold key terms.** But sparingly -- if everything is bold, nothing is.
- **Keep paragraphs short.** 3-4 sentences max. One idea per paragraph.

## Active Voice

**Don't write this:**

> The configuration file should be updated by the developer before the deployment is initiated.

**Write this instead:**

> Update the config file before deploying.

Active voice is shorter, clearer, and tells the reader who does what.

### Passive to Active

| Passive | Active |
|---------|--------|
| "The request is validated by the middleware" | "The middleware validates the request" |
| "Errors should be handled gracefully" | "Handle errors gracefully" |
| "The database was migrated" | "We migrated the database" (or just "Migrate the database") |

## Second Person

Address the reader directly. "You" is clearer than "the user" or "one."

- "Configure your API key" not "The user should configure their API key"
- "Run `make test`" not "The developer runs `make test`"

In step-by-step instructions, imperative mood works best: "Run this command. Open this file. Add this line."

## Consistent Terminology

Pick one term and stick with it. Don't alternate between:
- "user" and "customer" and "account holder"
- "endpoint" and "route" and "path"
- "config" and "configuration" and "settings"

Create a terminology table in your project's style guide if this becomes a problem.

## See Also

- `hub/writing-great-readmes.md` -- Style principles applied to READMEs
- `hub/error-messages.md` -- Style principles applied to errors
- `entities/style-checklist.md` -- Pre-publish checklist
- `guides/creating-a-style-guide.md` -- How to create a style guide for your team
- `research/writing-for-international-audiences.md` -- Plain English for translation
