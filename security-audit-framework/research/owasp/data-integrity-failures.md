---
title: "A08:2025 — Data Integrity Failures"
tags: [owasp, integrity, deserialization, ci-cd, updates]
content_type: research
domain: security
---

# A08:2025 — Data Integrity Failures

Relates to code and infrastructure that does not protect against integrity violations. Includes insecure deserialization, untrusted CI/CD pipelines, and auto-update without verification.

## Common Failures

- Insecure deserialization of untrusted data
- CI/CD pipelines without integrity verification
- Auto-updates without signature verification
- Unsigned or unverified software artifacts

## Insecure Deserialization

### The Risk

Deserialization of untrusted data can lead to remote code execution. In Python, the `pickle` module can instantiate arbitrary objects and execute code. In Node.js, libraries like `node-serialize` have similar risks. In Java, native deserialization (`ObjectInputStream`) is a common attack vector.

**Dangerous functions to search for in code review:**
- Python: `pickle.loads()`, `yaml.load()` (without SafeLoader)
- Node.js: `node-serialize.unserialize()`, `js-yaml.load()` (default)
- Java: `ObjectInputStream.readObject()`
- PHP: `unserialize()`

### Secure: Use JSON with Schema Validation

```python
# SECURE — Use JSON, which cannot execute code during parsing
import json

def load_user_data(serialized_data):
    data = json.loads(serialized_data)
    if not isinstance(data, dict):
        raise ValueError("Expected object")
    if 'user_id' not in data:
        raise ValueError("Missing required field: user_id")
    return data
```

```javascript
// SECURE — JSON parse + schema validation with ajv
const Ajv = require('ajv');
const ajv = new Ajv();

const schema = {
  type: 'object',
  properties: {
    userId: { type: 'string' },
    name: { type: 'string', maxLength: 100 },
  },
  required: ['userId', 'name'],
  additionalProperties: false,
};

app.post('/data', (req, res) => {
  let parsed;
  try {
    parsed = JSON.parse(req.body.data);
  } catch (e) {
    return res.status(400).json({ error: 'Invalid JSON' });
  }
  const valid = ajv.validate(schema, parsed);
  if (!valid) {
    return res.status(400).json({ error: 'Schema validation failed' });
  }
  // Process validated data
});
```

### Secure: YAML SafeLoader

```python
# SECURE — SafeLoader prevents arbitrary object instantiation
import yaml

data = yaml.safe_load(yaml_string)  # Only allows basic Python types
```

## CI/CD Integrity

### Vulnerable: Unpinned GitHub Actions — DO NOT USE

```yaml
# VULNERABLE — Tag can be moved to a malicious commit by attacker
- uses: actions/checkout@v4
- uses: some-org/deploy-action@main
```

### Secure: SHA-Pinned Actions

```yaml
# SECURE — Pinned to exact commit SHA, immutable reference
- uses: actions/checkout@b4ffde65f46336ab88eb53be808477a3936bae11 # v4.1.1
- uses: some-org/deploy-action@a1b2c3d4e5f6 # v2.0.0 verified
```

## How to Test

1. Search codebase for dangerous deserialization functions (see list above)
2. Check CI/CD configs for unpinned actions or orbs
3. Verify software updates use signature verification
4. Review deserialization points for type/schema validation
5. Run Semgrep with deserialization rules

## See Also

- hub/owasp-top-10.md
- research/owasp/supply-chain-failures.md
- research/dependencies/ci-cd-scanning.md
