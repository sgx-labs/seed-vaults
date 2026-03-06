---
title: "API Documentation That Developers Love"
tags: [guide, documentation, developer-experience, dx, examples, onboarding]
content_type: guide
domain: api-design
---

# API Documentation That Developers Love

## Overview

Good API docs get developers from zero to first successful API call in under 5 minutes. Great docs keep them coming back because they can find answers faster than asking for help. This guide covers what to include and how to structure it.

## The 5-Minute Test

Can a developer who has never seen your API:
1. Find the docs? (within 30 seconds)
2. Get an API key? (within 2 minutes)
3. Make their first successful request? (within 5 minutes)

If not, your docs need work.

## Essential Sections

### 1. Quick Start (Most Important)

The first thing developers see. Three steps max:

```markdown
## Quick Start

1. Get your API key:
   Sign up at https://dashboard.example.com and copy your key.

2. Make your first request:
   curl -H "Authorization: Bearer YOUR_API_KEY" \
     https://api.example.com/v1/users/me

3. You should see:
   {"id": "usr_abc", "name": "Your Name", "email": "you@test.com"}
```

Include a complete, copy-pasteable example. Not pseudocode. A real curl command that works.

### 2. Authentication

How to get a token, how to send it, what errors look like:

```markdown
## Authentication

Include your API key in the Authorization header:

    Authorization: Bearer sk_live_abc123

If the key is missing or invalid, you'll get:

    401 {"title": "Invalid API Key", "status": 401}
```

### 3. Endpoint Reference

For each endpoint, include:
- URL and method
- Description (one sentence)
- Parameters (name, type, required, description)
- Request example
- Response example
- Error cases

```markdown
## Create a Task

POST /v1/tasks

Creates a new task in the specified project.

### Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| title | string | Yes | Task title (max 200 chars) |
| description | string | No | Task description |
| status | string | No | One of: todo, in_progress, done. Default: todo |

### Example

Request:
  curl -X POST https://api.example.com/v1/tasks \
    -H "Authorization: Bearer YOUR_API_KEY" \
    -H "Content-Type: application/json" \
    -d '{"title": "Buy groceries", "status": "todo"}'

Response (201 Created):
  {"id": "tsk_abc", "title": "Buy groceries", "status": "todo", ...}
```

### 4. Error Reference

Document every error code your API returns:

```markdown
## Errors

| Code | HTTP Status | Meaning |
|------|-------------|---------|
| VALIDATION_ERROR | 422 | Request body failed validation |
| NOT_FOUND | 404 | Resource doesn't exist |
| DUPLICATE_EMAIL | 409 | Email already registered |
| RATE_LIMITED | 429 | Too many requests |
```

### 5. Changelog

What changed and when. Developers need to know if an update affects them.

```markdown
## Changelog

### 2025-01-15
- Added `description` field to Task responses
- Increased rate limit from 100 to 200 requests/minute

### 2025-01-01
- BREAKING: Renamed `name` to `full_name` on User responses (v2 only)
```

## Documentation Anti-Patterns

1. **No examples** -- developers learn by example, not by reading specs
2. **Outdated examples** -- worse than no examples. Test your docs in CI.
3. **Requiring signup to read docs** -- let developers evaluate before committing
4. **No error documentation** -- developers spend most time debugging errors
5. **Auto-generated only** -- generated docs are a starting point, not the product. Add guides and examples.
6. **Separate from code** -- docs that live far from code get stale. Keep them close.

## Tools

| Approach | Tool | Best For |
|----------|------|----------|
| OpenAPI + static | Redoc | Beautiful single-page reference |
| OpenAPI + interactive | Swagger UI | Try-it-out experience |
| Hosted platform | ReadMe, Mintlify | Full developer portal |
| Markdown + SSG | Docusaurus, MkDocs | Custom docs site |

## See Also

- `guides/writing-openapi-spec.md` -- OpenAPI as documentation foundation
- `research/openapi-best-practices.md` -- Spec that generates great docs
- `hub/error-handling.md` -- Errors worth documenting
