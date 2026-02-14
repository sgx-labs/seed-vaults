---
title: "A05:2025 — Cryptographic Failures"
tags: [owasp, cryptography, encryption, hashing, tls, aes-256, csprng, key-management]
content_type: research
domain: security
---

# A05:2025 — Cryptographic Failures

Previously "Sensitive Data Exposure." Focuses on failures related to cryptography that lead to data exposure or system compromise.

## Common Failures

- Transmitting data in plaintext (HTTP, FTP, SMTP without TLS)
- Using weak or deprecated algorithms (MD5, SHA1, DES, RC4)
- Hardcoded encryption keys in source code
- Missing encryption at rest for sensitive data
- Poor key management (keys stored alongside encrypted data)

## Vulnerable: Weak Hashing (Node.js) — DO NOT USE

```javascript
// VULNERABLE — MD5 is broken, produces trivial collisions
const crypto = require('crypto');
const hash = crypto.createHash('md5').update(password).digest('hex');
// Even SHA-256 is unsuitable for passwords (too fast, enables brute force)
```

## Secure: Proper Password Hashing

```javascript
// SECURE — bcrypt with appropriate cost factor
const bcrypt = require('bcrypt');
const SALT_ROUNDS = 12;

// Hashing
const hash = await bcrypt.hash(password, SALT_ROUNDS);

// Verification (constant-time comparison built in)
const match = await bcrypt.compare(inputPassword, storedHash);
```

```python
# SECURE — Argon2id (recommended for new implementations)
from argon2 import PasswordHasher
ph = PasswordHasher(
    time_cost=3,
    memory_cost=65536,  # 64 MB
    parallelism=4
)
hash_value = ph.hash(password)
# Verify
try:
    ph.verify(hash_value, input_password)
except argon2.exceptions.VerifyMismatchError:
    pass  # Invalid password
```

## Vulnerable: Hardcoded Encryption Key — DO NOT USE

```python
# VULNERABLE — Key in source code is exposed in version control
from cryptography.fernet import Fernet
key = b'EXAMPLE_HARDCODED_KEY_000000000000000000000='
cipher = Fernet(key)
```

## Secure: Key from Secrets Manager

```python
# SECURE — Key loaded from external secrets manager
import boto3
from cryptography.fernet import Fernet

def get_encryption_key():
    client = boto3.client('secretsmanager')
    response = client.get_secret_value(SecretId='app/encryption-key')
    return response['SecretString'].encode()

cipher = Fernet(get_encryption_key())
```

## Encryption Standards Reference

| Purpose | Recommended | Avoid |
|---------|-------------|-------|
| Symmetric encryption | AES-256-GCM | DES, 3DES, RC4, ECB mode |
| Password hashing | Argon2id, bcrypt | MD5, SHA1, SHA256 (plain) |
| TLS | TLS 1.3 (minimum 1.2) | SSL, TLS 1.0, TLS 1.1 |
| Asymmetric | RSA-2048+, Ed25519 | RSA-1024, DSA |
| Integrity hashing | SHA-256, SHA-3 | MD5, SHA-1 |
| Random generation | CSPRNG (crypto/rand) | Math.random(), rand() |

## Vulnerable: Insecure Random — DO NOT USE

```javascript
// VULNERABLE — Math.random() is not cryptographically secure
const token = Math.random().toString(36).substring(2);
```

## Secure: Cryptographic Random

```javascript
// SECURE — Node.js crypto module uses OS CSPRNG
const crypto = require('crypto');
const token = crypto.randomBytes(32).toString('hex');
```

## How to Test

1. Check TLS configuration with SSL Labs (ssllabs.com)
2. Search codebase for MD5, SHA1, DES, hardcoded key patterns
3. Verify passwords use Argon2id or bcrypt (not plain hash functions)
4. Check that HTTPS is enforced (HSTS header present)
5. Verify encryption keys are not stored alongside encrypted data

## See Also

- hub/owasp-top-10.md
- hub/authentication.md — Password storage
- research/authentication/password-hashing.md
- research/infrastructure/secrets-management.md
