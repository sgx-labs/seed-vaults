---
title: "API Documentation"
tags: [api-docs, openapi, swagger, reference, examples-first, versioning]
content_type: hub
domain: technical-writing
---

# API Documentation

## The Examples-First Approach

Developers don't read API docs top-to-bottom. They scan for an example that matches their use case, copy it, modify it, and move on. Design your docs around this behavior.

**Don't write this:**

```
The /users endpoint accepts GET requests with optional query
parameters for filtering. The response is a JSON object
containing a data array of user objects. Each user object
contains the following fields: id (string), name (string),
email (string), created_at (ISO 8601 timestamp).
```

**Write this instead:**

```
GET /users?role=admin&limit=10

Response:
{
  "data": [
    {"id": "usr_1", "name": "Alice", "email": "a@co.com", "created_at": "2025-01-15T10:00:00Z"}
  ],
  "has_more": true,
  "next_cursor": "abc123"
}
```

The example communicates everything the paragraph tried to say, and it's copy-pasteable.

## What API Docs Need

1. **Authentication** -- how to get credentials and include them in requests
2. **Base URL** -- including environment variants (staging, production)
3. **Endpoints** -- grouped by resource, with method, path, and description
4. **Request examples** -- real curl commands or SDK snippets that actually work
5. **Response examples** -- full JSON responses, not schemas
6. **Error responses** -- what errors look like and what causes them
7. **Rate limits** -- what the limits are and what happens when you hit them
8. **Changelog** -- what changed and when

## OpenAPI as Source of Truth

Write your OpenAPI spec first, then generate docs from it. Not the other way around.

Benefits:
- Spec stays in sync with code (or generates from code)
- Client SDKs auto-generate from the spec
- Interactive "try it" experiences from tools like Swagger UI, Redoc, Scalar
- Contract testing catches spec drift

Keep the spec in your repo alongside the code. See `research/documentation-as-code.md`.

## Versioning Your Docs

When your API versions, your docs must version too. Options:

- **URL-based versions** (`/v1/docs`, `/v2/docs`) -- simplest, clearest
- **Dropdown selector** -- most doc tools support version switching
- **Diff-based changelogs** -- show what changed between versions

Always keep the previous version's docs accessible. Developers on v1 still need docs.

## Common API Doc Mistakes

- **Outdated examples.** Worse than no docs. Test your examples in CI.
- **Missing error docs.** Developers spend more time debugging than building. Document every error.
- **No authentication guide.** "Use Bearer token" is not enough. Show the full flow.
- **Schema without examples.** A JSON Schema is not documentation. Show real responses.
- **Buried quickstart.** The first thing a developer sees should be a working request.

## See Also

- `hub/error-messages.md` -- Writing error responses that help
- `hub/changelogs.md` -- Documenting API changes
- `hub/diataxis-framework.md` -- API docs as reference documentation
- `research/documentation-as-code.md` -- Keeping docs in the repo
- `research/docs-tooling-landscape.md` -- Tools for generating API docs
- `entities/common-documentation-templates.md` -- API doc templates
