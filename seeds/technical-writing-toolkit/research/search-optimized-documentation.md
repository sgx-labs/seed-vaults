---
title: "Search-Optimized Documentation"
tags: [search, findability, information-architecture, seo, navigation, documentation]
content_type: research
domain: technical-writing
---

# Search-Optimized Documentation

## How People Search Docs

Developers don't browse documentation linearly. They:
1. Google an error message or question
2. Land on a page
3. Scan for their answer (5-10 seconds)
4. Leave if they don't find it

Your docs must be optimized for this behavior, not for cover-to-cover reading.

## Making Docs Findable

### Use the Words People Search For

Developers search for the words they already know, not the words you chose for your API.

**Don't write this:**

> "Implement rate throttling via the RequestGovernor middleware."

**Write this instead:**

> "Rate limiting — limit how many requests a client can make per minute."

Include synonyms. If your feature is called "RequestGovernor" internally but developers search for "rate limiting", put "rate limiting" in the title and first paragraph.

### Title Pages for Search

Titles should match common search queries:

| Poor title | Better title |
|-----------|-------------|
| "Configuration" | "How to configure authentication" |
| "Advanced Features" | "Rate limiting, caching, and webhooks" |
| "Getting Started" | "Install and run your first query in 5 minutes" |
| "FAQ" | "Common errors and how to fix them" |

### Put the Answer First

Don't bury the answer under three paragraphs of context. Developers scanning your page need the answer in the first sentence.

**Don't write this:**

> The upload feature was introduced in version 2.3 to support various use cases including profile images, document attachments, and file exports. The maximum file size depends on your plan tier and can be configured in the admin panel. By default, files up to 10MB are supported.

**Write this instead:**

> Maximum file size: 10MB by default. Configure in Admin > Settings > Uploads.

### Structure for Scanning

- **Use descriptive headings.** "How to reset your API key" not "API Keys."
- **Use anchor links.** Deep links to specific sections let people land exactly where they need to.
- **Use code blocks for searchable content.** Error messages in code blocks are more scannable than inline text.
- **Include the error message.** If a page explains how to fix an error, include the exact error text so search engines index it.

## Information Architecture

### Flat Over Deep

Prefer shallow hierarchies. Two clicks to any page is ideal.

```
# Good: flat
docs/
  authentication.md
  rate-limiting.md
  webhooks.md
  error-codes.md

# Bad: deep
docs/
  concepts/
    core/
      authentication/
        overview.md
```

### One Topic Per Page

Each page should answer one question or explain one concept. If a page covers three topics, people searching for topic #2 might not find it because the page title only mentions topic #1.

### Predictable URLs

```
/docs/authentication          (not /docs/auth-guide-v2-final)
/docs/rate-limiting            (not /docs/advanced/throttling)
/api/users                     (not /api/v2/user-management)
```

## Measuring Findability

- **Search analytics.** What are people searching for? If "rate limiting" gets 100 searches and zero results, you have a gap.
- **Zero-result queries.** These are documentation bugs. Track and fix them.
- **Time to first success.** How long from landing on docs to completing a task?
- **Support tickets.** If people ask support questions your docs answer, the docs aren't findable.

## See Also

- `hub/writing-style-guide.md` -- Clarity helps findability
- `hub/diataxis-framework.md` -- Organizing docs by purpose
- `research/measuring-documentation-quality.md` -- Metrics for doc effectiveness
- `research/documentation-debt.md` -- Findability degrades with doc debt
