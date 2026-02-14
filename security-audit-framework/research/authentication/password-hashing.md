---
title: "Password Hashing — Secure Implementation"
tags: [authentication, passwords, hashing, bcrypt, argon2, scrypt, pbkdf2, cost-factor]
content_type: research
domain: security
---

# Password Hashing — Secure Implementation

Passwords must be stored using a purpose-built password hashing function. General-purpose hash functions (SHA-256, MD5) are NOT suitable.

## Why General Hashing Fails

General-purpose hashes are designed to be FAST. Password hashing must be SLOW to resist brute-force attacks. A modern GPU can compute billions of SHA-256 hashes per second but only thousands of bcrypt hashes.

## Algorithm Comparison

| Algorithm | Memory-Hard | GPU Resistant | Max Input | Recommendation |
|-----------|------------|---------------|-----------|----------------|
| Argon2id | Yes | Yes | Unlimited | Best choice for new projects |
| bcrypt | No | Moderate | 72 bytes | Proven, widely supported |
| scrypt | Yes | Yes | Unlimited | Good alternative to Argon2 |
| PBKDF2-SHA256 | No | No | Unlimited | Legacy only, 600K+ iterations |

## Argon2id (Recommended)

```python
# Python — argon2-cffi
from argon2 import PasswordHasher

ph = PasswordHasher(
    time_cost=3,        # Number of iterations
    memory_cost=65536,  # 64 MB of memory
    parallelism=4,      # Number of threads
    hash_len=32,        # Output hash length
    salt_len=16,        # Salt length
)

# Hash a password
hashed = ph.hash("user_password_here")

# Verify a password
try:
    ph.verify(hashed, "user_input_here")
    # Check if rehash is needed (parameters changed)
    if ph.check_needs_rehash(hashed):
        new_hash = ph.hash("user_input_here")
        # Update stored hash
except argon2.exceptions.VerifyMismatchError:
    # Invalid password
    pass
```

## bcrypt

```javascript
// Node.js — bcrypt
const bcrypt = require('bcrypt');
const SALT_ROUNDS = 12; // Minimum 10, recommended 12+

// Hash
const hash = await bcrypt.hash(password, SALT_ROUNDS);

// Verify (constant-time comparison is built in)
const isValid = await bcrypt.compare(inputPassword, hash);
```

```go
// Go — golang.org/x/crypto/bcrypt
import "golang.org/x/crypto/bcrypt"

// Hash
hash, err := bcrypt.GenerateFromPassword([]byte(password), 12)

// Verify
err = bcrypt.CompareHashAndPassword(hash, []byte(inputPassword))
if err != nil {
    // Invalid password
}
```

## bcrypt Gotchas

- **72-byte limit**: bcrypt silently truncates input at 72 bytes. For long passwords, pre-hash with SHA-256 then bcrypt the result.
- **Null bytes**: Some implementations truncate at the first null byte.
- **Cost factor**: Increase by 1 every ~18 months as hardware improves.

## Migration Strategy

When upgrading from a weaker algorithm:

1. Store the algorithm version with the hash
2. On successful login, check if rehash is needed
3. If using old algorithm, verify with old, rehash with new
4. Set a deadline to force password resets for remaining old hashes

## How to Test

1. Verify password hashes are NOT stored as plain SHA/MD5
2. Check bcrypt cost factor is >= 12
3. Verify Argon2 memory cost is >= 64MB
4. Test that password verification uses constant-time comparison
5. Check for the 72-byte bcrypt truncation issue

## See Also

- hub/authentication.md
- research/owasp/cryptographic-failures.md
- research/authentication/password-reset-flows.md
