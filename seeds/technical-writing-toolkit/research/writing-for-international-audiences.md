---
title: "Writing for International Audiences"
tags: [internationalization, i18n, plain-english, translation, global, documentation]
content_type: research
domain: technical-writing
---

# Writing for International Audiences

## Why This Matters

If your documentation is read by non-native English speakers -- or might be translated -- how you write is as important as what you write. Clear, simple English isn't dumbed-down English. It's accessible English.

## Plain English Principles

### Use Simple Words

| Instead of | Write |
|-----------|-------|
| utilize | use |
| terminate | stop, end |
| facilitate | help |
| endeavor | try |
| commence | start |
| subsequently | then |
| in lieu of | instead of |
| notwithstanding | despite |

### Use Short Sentences

**Don't write this:**

> When configuring the application for production deployment, it is important to ensure that all environment variables have been properly set, the database migrations have been run, and the application has been tested against the production environment, before initiating the deployment process.

**Write this instead:**

> Before deploying to production:
> 1. Set all environment variables.
> 2. Run database migrations.
> 3. Test against the production environment.

Target 15-20 words per sentence. If a sentence has a comma, consider splitting it.

### Avoid Idioms and Colloquialisms

These don't translate well -- literally or figuratively:

| Avoid | Write instead |
|-------|-------------|
| "out of the box" | "by default" or "without configuration" |
| "under the hood" | "internally" or "in the implementation" |
| "boilerplate" | "template code" or "standard setup" |
| "gotcha" | "common mistake" or "known issue" |
| "ball is in your court" | "you need to take action" |
| "low-hanging fruit" | "easy improvement" or "quick win" |
| "deep dive" | "detailed analysis" |
| "at the end of the day" | "ultimately" |

### Avoid Cultural References

- Sports metaphors ("home run", "slam dunk", "moving the goalposts")
- Food metaphors ("half-baked", "eat your own dog food")
- Culture-specific humor or sarcasm

### Be Explicit About Subjects

**Don't write this:**

> It should be configured before running.

**Write this instead:**

> Configure the database before running the application.

Ambiguous pronouns ("it", "this", "that") are harder for non-native speakers.

## Translation-Ready Writing

If your docs might be translated (by humans or machines):

- **Keep sentences simple.** Complex grammar creates ambiguous translations.
- **Use consistent terminology.** Don't alternate between "config", "configuration", and "settings" for the same concept.
- **Avoid text in images.** Images can't be translated. Use text-based diagrams (Mermaid, ASCII) or keep images language-neutral.
- **Don't concatenate strings.** "Click {action} to {result}" works in English but word order varies across languages.
- **Leave room for expansion.** German text is typically 30% longer than English. UI labels and button text need space to grow.

## Date and Number Formats

| Format | US | International |
|--------|-----|---------------|
| Date | 03/15/2025 | 2025-03-15 (ISO 8601) |
| Time | 3:30 PM | 15:30 |
| Number | 1,000.50 | varies (1.000,50 in many countries) |
| Currency | $100 | 100 USD |

Always use ISO 8601 for dates in documentation. It's unambiguous.

## See Also

- `hub/writing-style-guide.md` -- General style principles
- `research/documentation-as-code.md` -- i18n support in doc tools
- `entities/style-checklist.md` -- Internationalization items on the checklist
- `entities/accessibility-in-documentation.md` -- Accessibility and internationalization overlap
