---
title: "Authorization Patterns"
tags: [authorization, rbac, abac, scopes, permissions, access-control, security]
content_type: hub
domain: api-design
---

# Authorization Patterns

## Overview

Authorization determines what an authenticated user is allowed to do -- "you are who you say you are, but can you do THIS?" The three main models are RBAC (role-based), ABAC (attribute-based), and scope-based. Most APIs use RBAC or a combination.

## RBAC (Role-Based Access Control)

Users get assigned roles. Roles have permissions. Simple, well-understood, covers most cases.

```
Roles:
  admin   -> [read, write, delete, manage_users]
  editor  -> [read, write]
  viewer  -> [read]

Check:
  if user.role has "write" permission -> allow POST/PUT/PATCH
  if user.role has "delete" permission -> allow DELETE
```

**When to use**: Most applications. Clear hierarchy of access levels. Internal tools, SaaS products, admin panels.

**When NOT to use**: When permissions depend on data relationships (e.g., "can edit only their own posts"), highly dynamic permission models, complex multi-tenant scenarios.

**Best practices**:
- Keep the number of roles small (3-7 for most apps)
- Assign permissions to roles, not directly to users
- Support custom roles for enterprise customers
- Check permissions, not roles, in your code (`user.can("write")` not `user.role == "admin"`)

## ABAC (Attribute-Based Access Control)

Permissions depend on attributes of the user, resource, action, and environment.

```
Policy:
  ALLOW if:
    user.department == resource.department
    AND action == "read"
    AND time.hour >= 9 AND time.hour <= 17

  ALLOW if:
    user.role == "manager"
    AND resource.owner == user.id
    AND action in ["read", "write", "delete"]
```

**When to use**: Complex authorization rules, multi-tenant systems, regulatory compliance (HIPAA, GDPR), when permissions depend on data relationships.

**When NOT to use**: Simple apps where RBAC suffices. ABAC adds significant complexity. Don't use it just because it's more powerful.

## Scope-Based (OAuth2 Scopes)

Scopes limit what an OAuth2 token can do. They restrict the token, not the user.

```
Token scopes: ["read:users", "write:orders"]

Request: DELETE /users/123
Check: token has "delete:users" scope? No -> 403 Forbidden
```

**When to use**: OAuth2 APIs, third-party integrations, APIs where tokens should have narrower permissions than the user.

**When NOT to use**: First-party apps where the token always has full user permissions. Scopes add overhead without value if every token gets all scopes.

**Naming conventions**:
```
read:resource     # read-only access
write:resource    # create and update
delete:resource   # delete access
admin:resource    # full access including management
```

## Combining Models

Most production APIs combine approaches:

```
1. OAuth2 scopes gate the token -> "can this token access user data?"
2. RBAC gates the user -> "does this user have the editor role?"
3. ABAC gates the resource -> "does this user own this resource?"
```

Example middleware chain:
```
request -> [verify token] -> [check scope] -> [check role] -> [check ownership] -> handler
```

## Common Patterns

### Resource Ownership
```
GET /posts/123
  -> Is user authenticated? (auth)
  -> Does token have read:posts scope? (scope)
  -> Does user have "reader" role? (RBAC)
  -> Is user the author OR is post published? (ABAC)
```

### Admin Override
```
if user.role == "admin":
    allow all actions
else:
    check specific permissions
```

### Deny by Default
Always deny unless explicitly allowed. Never rely on "if not forbidden, then allowed."

## When to Use What

| Scenario | Model | Why |
|----------|-------|-----|
| Simple SaaS app | RBAC | 3-5 roles cover all access patterns |
| Public API with third-party apps | Scopes + RBAC | Scopes limit tokens, RBAC limits users |
| Multi-tenant enterprise | ABAC + RBAC | Tenant isolation requires attribute checks |
| Content platform (users own content) | RBAC + ownership | Role for broad access, ownership for specific resources |
| Healthcare/finance (compliance) | ABAC | Regulatory rules map to attribute policies |

## See Also

- `hub/authentication.md` -- Authentication comes before authorization
- `research/oauth2-flows.md` -- OAuth2 scopes in practice
- `research/api-security-checklist.md` -- Security checklist including authorization
