---
title: "Code Comments Philosophy"
tags: [comments, self-documenting, code-quality, readability, documentation]
content_type: hub
domain: technical-writing
---

# Code Comments Philosophy

## The Rule

Comment *why*, not *what*. Code tells you what it does. Comments tell you why it does it that way.

## When to Comment

**Comment when:**
- The code does something non-obvious and a reader would wonder why
- You're working around a bug in a library or platform
- The algorithm has a subtle edge case that isn't immediately apparent
- You chose a slower approach deliberately (for correctness, compatibility, etc.)
- A business rule drives the logic and the rule isn't self-evident

**Don't comment when:**
- The code already says what the comment says
- A better variable or function name would eliminate the need
- You're explaining *what* a standard library function does

## Before and After

**Don't write this:**

```python
# Increment counter by 1
counter += 1

# Check if user is admin
if user.role == "admin":

# Loop through the list of items
for item in items:
```

**Write this instead:**

```python
# Rate limit resets after 60s, but we add 1s buffer to avoid
# race conditions with the upstream API's clock skew
time.sleep(61)

# Users created before 2024-03-01 have legacy permissions
# that bypass the new RBAC system. See ADR-012.
if user.created_at < LEGACY_CUTOFF:

# Process in reverse order because child records must be
# deleted before parents due to foreign key constraints
for item in reversed(items):
```

## The Self-Documenting Code Myth

"Good code doesn't need comments" is half-true. Good code doesn't need comments that explain *what it does*. But no amount of clean code can explain:

- **Why** this approach was chosen over a simpler one
- **Why** this edge case matters
- **What** business rule drives this logic
- **What** external constraint forced this design

The developers who say they never need comments are either writing trivial code or leaving traps for their successors.

## Types of Useful Comments

| Type | Example |
|------|---------|
| **Rationale** | `// We use polling instead of webhooks because the vendor's webhook delivery is unreliable (drops ~2% of events)` |
| **Warning** | `// WARNING: This function is not thread-safe. Always call within a mutex lock.` |
| **Workaround** | `// HACK: The API returns 200 for deleted resources. Check the "deleted_at" field instead of status code.` |
| **Context link** | `// See ADR-007 for why we denormalize this data` |
| **Performance** | `// O(n^2) but n is always < 50. Linear search is faster than building a hash map for small n.` |
| **TODO** | `// TODO(alice): Remove after migration completes (tracking issue #234)` |

## Doc Comments vs Inline Comments

- **Doc comments** (above functions/classes): Explain the contract -- what it does, what it expects, what it returns. These ARE about *what*.
- **Inline comments** (inside functions): Explain *why* the code works the way it does. These are about *why*.

```go
// ValidateOrder checks that an order meets business rules for submission.
// Returns an error describing the first validation failure, or nil.
// Does NOT check inventory availability -- that happens at checkout.
func ValidateOrder(order *Order) error {
    // Minimum order value is $10 to cover payment processing fees.
    // See pricing decision 2024-Q3.
    if order.Total < 1000 { // cents
        return ErrBelowMinimum
    }
}
```

## Comments That Rot

Comments go stale faster than code. Reduce rot by:

- **Keeping comments close to the code they describe.** A comment 20 lines above the relevant code will be orphaned during refactoring.
- **Avoiding restating the code.** If the code changes, restated comments become lies.
- **Using TODO with tracking.** `// TODO(name): reason (issue #123)` is findable and actionable.
- **Reviewing comments during code review.** If the code changed, did the comment need to change too?

## See Also

- `hub/writing-style-guide.md` -- Clarity principles that apply to comments
- `hub/error-messages.md` -- Writing helpful error text in code
- `hub/api-documentation.md` -- Doc comments for public APIs
- `research/documentation-as-code.md` -- Comments as part of the docs-as-code approach
