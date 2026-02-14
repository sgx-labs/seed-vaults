---
title: "A04:2025 — Injection"
tags: [owasp, injection, sql-injection, xss, command-injection, nosql]
content_type: research
domain: security
---

# A04:2025 — Injection

Injection attacks occur when untrusted data is sent to an interpreter as part of a command or query. This covers SQL injection, XSS, command injection, and NoSQL injection.

## SQL Injection

### Vulnerable (Node.js) — DO NOT USE

```javascript
// VULNERABLE — String concatenation allows SQL injection
app.get('/users', async (req, res) => {
  const query = `SELECT * FROM users WHERE name = '${req.query.name}'`;
  const result = await db.query(query);
  res.json(result);
});
// Attack input: name=' OR '1'='1  -> Returns all users
```

### Secure: Parameterized Queries

```javascript
// SECURE — Parameterized query
app.get('/users', async (req, res) => {
  const query = 'SELECT * FROM users WHERE name = $1';
  const result = await db.query(query, [req.query.name]);
  res.json(result);
});
```

### Secure: Python with SQLAlchemy

```python
# SECURE — ORM with parameterized queries
user = db.session.query(User).filter(User.name == request.args['name']).first()

# SECURE — Raw SQL with parameters
result = db.session.execute(
    text("SELECT * FROM users WHERE name = :name"),
    {"name": request.args['name']}
)
```

## Cross-Site Scripting (XSS)

### Vulnerable (Express) — DO NOT USE

```javascript
// VULNERABLE — Unescaped user input rendered in HTML
app.get('/search', (req, res) => {
  res.send(`<h1>Results for: ${req.query.q}</h1>`);
});
// Attack input: q=<script>alert(document.cookie)</script>
```

### Secure: Output Encoding

```javascript
// SECURE — Use a template engine with auto-escaping
// EJS auto-escapes with <%= %>
res.render('search', { query: req.query.q });

// Or manually escape
const escapeHtml = require('escape-html');
res.send(`<h1>Results for: ${escapeHtml(req.query.q)}</h1>`);

// Plus CSP header to block inline scripts
app.use(helmet.contentSecurityPolicy({
  directives: { defaultSrc: ["'self'"], scriptSrc: ["'self'"] }
}));
```

## Command Injection

### Vulnerable (Python) — DO NOT USE

```python
# VULNERABLE — Shell execution with user input enables command injection
# An attacker can inject arbitrary commands via the filename parameter
# Example attack input: file="; rm -rf /"
import subprocess
filename = request.args['file']
subprocess.run(f"convert {filename} output.pdf", shell=True)
```

### Secure: No Shell, List Arguments

```python
# SECURE — No shell, arguments as a list (cannot inject commands)
import subprocess
import re
filename = request.args['file']

# Validate input first — allowlist safe characters only
if not re.match(r'^[a-zA-Z0-9_.-]+$', filename):
    abort(400, 'Invalid filename')

subprocess.run(['convert', filename, 'output.pdf'], check=True, shell=False)
```

## NoSQL Injection

### Vulnerable (MongoDB) — DO NOT USE

```javascript
// VULNERABLE — Object injection bypasses authentication
app.post('/login', async (req, res) => {
  const user = await db.collection('users').findOne({
    username: req.body.username,
    password: req.body.password
  });
  // Attack input: {"username": "admin", "password": {"$ne": ""}}
  // This matches any non-empty password for admin
});
```

### Secure: Type Validation

```javascript
// SECURE — Validate types before query
app.post('/login', async (req, res) => {
  const { username, password } = req.body;
  if (typeof username !== 'string' || typeof password !== 'string') {
    return res.status(400).json({ error: 'Invalid input' });
  }
  // Now safe to query (plus use bcrypt.compare for the password)
});
```

## How to Test

1. Try `' OR '1'='1` in text inputs and URL parameters
2. Try `<script>alert(1)</script>` in text fields and search boxes
3. Use Semgrep rules for injection patterns
4. Run OWASP ZAP active scan against your application
5. Use parameterized query linters (e.g., eslint-plugin-security)

## See Also

- hub/owasp-top-10.md
- hub/api-security.md — Input validation
- research/api-security/input-validation.md
