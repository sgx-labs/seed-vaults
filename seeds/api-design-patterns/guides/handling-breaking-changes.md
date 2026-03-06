---
title: "Handling Breaking Changes"
tags: [guide, breaking-changes, versioning, deprecation, migration, compatibility]
content_type: guide
domain: api-design
---

# Handling Breaking Changes

## Overview

Breaking changes are inevitable. The question isn't whether you'll need to make them -- it's how to manage them without losing client trust. This guide covers the full lifecycle from detection to deprecation.

## Step 1: Determine if the Change is Actually Breaking

| Change | Breaking? | Action |
|--------|----------|--------|
| Add a new field to response | No | Ship it |
| Add a new optional query parameter | No | Ship it |
| Add a new endpoint | No | Ship it |
| Remove a field from response | YES | Follow this guide |
| Rename a field | YES | Follow this guide |
| Change a field's type | YES | Follow this guide |
| Make an optional parameter required | YES | Follow this guide |
| Change error response format | YES | Follow this guide |
| Change status codes | YES | Follow this guide |
| Change authentication requirements | YES | Follow this guide |

**Rule**: If any existing client code would break or behave differently, it's breaking.

## Step 2: Explore Non-Breaking Alternatives

Before creating a new version, consider:

### Add, Don't Remove
```
// Instead of renaming "name" to "full_name":
// Keep both, deprecate the old one

Response v1: {"name": "Alice Johnson"}
Response v1 (updated): {"name": "Alice Johnson", "full_name": "Alice Johnson"}

// "name" is deprecated but still present
```

### New Endpoint
```
// Instead of changing /search behavior:
GET /search      -> old behavior (unchanged)
GET /search/v2   -> new behavior
```

### Feature Flag
```
GET /users?format=extended
// Returns new format when flag is present, old format when absent
```

## Step 3: If You Must Break, Plan the Migration

### Timeline

```
Week 0:  Announce the change (changelog, email, in-API deprecation headers)
Week 4:  Release new version alongside old version
Week 8:  Monitor usage of old version
Week 12: Send reminders to remaining old-version users
Week 16: Begin rate-limiting old version
Week 20: Shut down old version (or extend if significant usage remains)
```

For public APIs, extend this to 6-12 months.

### Communication

1. **Changelog entry** -- what changed, why, and how to migrate
2. **Deprecation headers** in old API responses:
   ```
   Deprecation: true
   Sunset: Sat, 01 Jun 2025 00:00:00 GMT
   Link: <https://api.example.com/v2/users>; rel="successor-version"
   ```
3. **Email notification** to all API key holders
4. **Migration guide** with before/after examples

## Step 4: Write a Migration Guide

For each breaking change, document:

```
## Change: "name" field split into "first_name" and "last_name"

### Before (v1)
  GET /v1/users/123
  Response: {"name": "Alice Johnson"}

### After (v2)
  GET /v2/users/123
  Response: {"first_name": "Alice", "last_name": "Johnson"}

### Migration steps:
  1. Update your code to use first_name and last_name
  2. Change your base URL from /v1/ to /v2/
  3. Test your integration
  4. Remove any references to the "name" field
```

## Step 5: Monitor the Transition

Track old version usage:
- Requests per day to deprecated endpoints
- Number of unique API keys still using old version
- Error rate on old vs new version

Don't shut down the old version while it still has significant traffic. "Significant" depends on your API -- 1% of a high-traffic API is still a lot of requests.

## Step 6: Shut Down Gracefully

When it's time:
1. Return 410 Gone (not 404) for removed endpoints
2. Include a message pointing to the new version
3. Keep the 410 response alive for at least 30 days
4. Log 410 responses to track any remaining clients

```json
{
  "type": "https://api.example.com/errors/version-sunset",
  "title": "API Version Removed",
  "status": 410,
  "detail": "API v1 was sunset on 2025-06-01. Please migrate to v2: https://docs.example.com/migration"
}
```

## See Also

- `hub/versioning.md` -- Versioning strategies
- `research/api-versioning-strategies.md` -- Detailed comparison
- `hub/error-handling.md` -- Error responses for deprecated endpoints
