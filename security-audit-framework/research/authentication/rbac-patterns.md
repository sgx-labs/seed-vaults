---
title: "Role-Based Access Control (RBAC) Patterns"
tags: [authentication, authorization, rbac, permissions, roles]
content_type: research
domain: security
---

# Role-Based Access Control (RBAC) Patterns

RBAC assigns permissions to roles, then roles to users. Simpler than attribute-based access control (ABAC) but sufficient for most applications.

## Core Concepts

- **User** — Identity that authenticates
- **Role** — Named collection of permissions (admin, editor, viewer)
- **Permission** — Specific action on a resource (posts:write, users:delete)
- **Resource** — What is being accessed (post, user, invoice)

## Database Schema

```sql
-- SECURE — Normalized RBAC schema
CREATE TABLE roles (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    name VARCHAR(50) UNIQUE NOT NULL,
    description TEXT
);

CREATE TABLE permissions (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    resource VARCHAR(50) NOT NULL,
    action VARCHAR(50) NOT NULL,
    UNIQUE(resource, action)
);

CREATE TABLE role_permissions (
    role_id UUID REFERENCES roles(id),
    permission_id UUID REFERENCES permissions(id),
    PRIMARY KEY (role_id, permission_id)
);

CREATE TABLE user_roles (
    user_id UUID REFERENCES users(id),
    role_id UUID REFERENCES roles(id),
    PRIMARY KEY (user_id, role_id)
);
```

## Middleware Implementation (Express.js)

```javascript
// SECURE — Permission-based authorization middleware
function requirePermission(resource, action) {
  return async (req, res, next) => {
    if (!req.user) {
      return res.status(401).json({ error: 'Authentication required' });
    }

    const hasPermission = await db.checkPermission(
      req.user.id, resource, action
    );

    if (!hasPermission) {
      logger.warn('auth.access_denied', {
        userId: req.user.id,
        resource,
        action,
        path: req.path,
      });
      return res.status(403).json({ error: 'Insufficient permissions' });
    }

    next();
  };
}

// Usage
app.get('/api/posts', requirePermission('posts', 'read'), getPosts);
app.post('/api/posts', requirePermission('posts', 'write'), createPost);
app.delete('/api/posts/:id', requirePermission('posts', 'delete'), deletePost);
app.get('/api/admin/users', requirePermission('users', 'admin'), listUsers);
```

## Python Decorator Pattern

```python
# SECURE — Permission decorator for Flask
from functools import wraps

def require_permission(resource, action):
    def decorator(f):
        @wraps(f)
        def decorated(*args, **kwargs):
            if not g.user:
                abort(401)
            if not check_permission(g.user.id, resource, action):
                logger.warning('auth.access_denied',
                               user_id=g.user.id,
                               resource=resource,
                               action=action)
                abort(403)
            return f(*args, **kwargs)
        return decorated
    return decorator

@app.route('/api/posts', methods=['DELETE'])
@login_required
@require_permission('posts', 'delete')
def delete_post():
    # Only reaches here if user has posts:delete permission
    pass
```

## Best Practices

1. **Deny by default** — No permission = no access
2. **Check server-side** — UI role checks are for UX only, not security
3. **Audit all denials** — Log who tried to access what
4. **Least privilege** — Start with minimum permissions, add as needed
5. **Separate roles from permissions** — Roles change; permissions are stable
6. **Cache with care** — Cache permissions but invalidate on role changes

## Common Mistakes

- Checking roles instead of permissions (`if user.role === 'admin'` is brittle)
- Hardcoding role names in business logic (use permissions as the abstraction)
- Not checking ownership (user has `posts:delete` but should only delete THEIR posts)
- Missing authorization on API endpoints that the UI "hides"

## How to Test

1. Access admin endpoints as a regular user — should get 403
2. Access another user's resources — should get 403
3. Verify role changes take effect immediately (or after cache TTL)
4. Test with no roles assigned — should have no access
5. Check that authorization errors are logged

## See Also

- hub/authentication.md
- research/owasp/broken-access-control.md
- research/authentication/session-management.md
