---
title: "API Versioning Strategies Compared"
tags: [versioning, breaking-changes, evolution, compatibility, semver, deprecation]
content_type: research
domain: api-design
---

# API Versioning Strategies Compared

## The Core Idea

Every API will change. Versioning lets you evolve your API without breaking existing clients. The debate isn't whether to version -- it's how. This note compares strategies with real-world examples and implementation details.

## Strategy 1: URL Path Versioning

```
GET /v1/users
GET /v2/users
```

### How Stripe Does It

Stripe uses date-based API versions in the URL, selectable per-account:
```
Stripe-Version: 2024-01-18
```

Each account is pinned to the version it was created with. Developers upgrade explicitly. Stripe maintains backward compatibility within a version and publishes detailed changelogs.

### Implementation

```
Router:
  /v1/* -> v1 handlers
  /v2/* -> v2 handlers

Shared:
  /v1/users -> UserController.listV1()
  /v2/users -> UserController.listV2()

Or with a version middleware:
  /users -> VersionRouter -> v1.UserController or v2.UserController
```

**Pros**: Trivially simple, clients always know their version, easy to test, cache-friendly.

**Cons**: Duplicate code between versions, multiple handler sets, old versions linger.

## Strategy 2: Header Versioning

```
GET /users
Accept: application/vnd.myapi.v2+json
```

### How GitHub Does It

GitHub uses the Accept header with a custom media type:
```
Accept: application/vnd.github.v3+json
```

They also support URL-based versioning as a convenience. Most developers use the URL version.

### Implementation

```
Middleware:
  version = parseAcceptHeader(request) or defaultVersion
  request.apiVersion = version

Handler:
  if request.apiVersion == 2:
    return v2Response(data)
  else:
    return v1Response(data)
```

**Pros**: Clean URLs, fine-grained (per-endpoint), supports content negotiation.

**Cons**: Hidden, easy to forget, harder to test in browser, caching needs `Vary: Accept`.

## Strategy 3: Query Parameter

```
GET /users?api-version=2
```

### How Azure Does It

Azure uses query parameters with date-based versions:
```
GET /subscriptions?api-version=2023-01-01
```

Required on every request. No default version.

### Implementation

```
Middleware:
  version = request.query["api-version"] or defaultVersion
  request.apiVersion = version
```

**Pros**: Visible, easy to change per-request, easy to test.

**Cons**: Semantic mismatch (query params are for filtering), pollutes URLs.

## Strategy 4: No Versioning (Additive Changes Only)

Never break the API. Only add new fields, endpoints, and capabilities.

```
V1: {"name": "Alice"}
V2: {"name": "Alice", "email": "alice@test.com"}  # new field added

Clients that don't know about "email" simply ignore it.
```

### How It Works

- New fields are always optional
- Never remove fields, only deprecate them
- Never change field types
- Never change the meaning of existing values
- Use feature flags for behavior changes

**Pros**: No version management overhead, clients never break, simplest operational model.

**Cons**: Schema grows forever, deprecated fields linger, sometimes you NEED a breaking change.

## What Counts as a Breaking Change?

| Change | Breaking? |
|--------|----------|
| Adding a new field to responses | No |
| Adding a new optional parameter | No |
| Adding a new endpoint | No |
| Removing a field from responses | Yes |
| Renaming a field | Yes |
| Changing a field's type | Yes |
| Making an optional parameter required | Yes |
| Changing error formats | Yes |
| Changing status codes for existing behavior | Yes |
| Changing authentication requirements | Yes |

## Deprecation Best Practices

1. **Announce early** -- 6-12 months before removal for public APIs
2. **Deprecation headers** -- `Deprecation: true` and `Sunset: Sat, 01 Jun 2025 00:00:00 GMT`
3. **Migration guide** -- document exactly what to change
4. **Usage monitoring** -- track who's still using deprecated versions
5. **Gradual sunset** -- rate limit old versions before removing them entirely

## Recommendation

For most APIs:
1. Start with **URL path versioning** (`/v1/`) -- it's the most widely understood
2. Practice **additive changes** -- most changes don't need a new version
3. Reserve new versions for genuine breaking changes
4. Support N-1 (current and previous version)
5. Sunset old versions with 6+ months notice

## See Also

- `hub/versioning.md` -- Overview of versioning approaches
- `guides/handling-breaking-changes.md` -- Managing breaking changes gracefully
