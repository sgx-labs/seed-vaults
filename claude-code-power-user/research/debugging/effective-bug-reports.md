---
title: "Getting Claude to Fix Bugs Faster"
tags: [debugging, bug-reports, prompting, workflow, error-messages, stack-traces, reproduction]
content_type: research
domain: engineering
---

# Getting Claude to Fix Bugs Faster

The difference between a 30-second fix and a 10-minute investigation is the quality of your bug report prompt.

---

## The Five Elements

| Element | Why It Matters |
|---|---|
| **Exact error message** | Claude pattern-matches against known issues instantly |
| **File and line number** | Eliminates search time |
| **Reproduction steps** | Establishes the trigger sequence |
| **What you expected** | Defines correct behavior |
| **What actually happened** | Defines the deviation |

### Template

```
I'm getting this error when I run [command]:

[paste exact error output here]

The error is in [file path], around line [number].

Steps to reproduce:
1. [step]
2. [step]

Expected: [what should happen]
Actual: [what does happen]
```

---

## Good vs Bad Bug Reports

**Bad -- vague:**
```
The tests are broken. Can you fix them?
```
Claude has to find which tests, what failure, which files. Triggers a broad search.

**Better -- some context:**
```
The unit tests in the auth module are failing. Something about a nil pointer.
```
Claude knows the area but must still find the test, run it, read output, and diagnose.

**Best -- full context:**
```
`make test` fails with:

--- FAIL: TestAuthMiddleware/expired_token (0.00s)
    auth_test.go:47: runtime error: invalid memory address or nil pointer dereference

The test is in internal/auth/auth_test.go, line 47.
I added the token expiry check in the last commit (see auth.go lines 82-95).
The test expects a 401 response but the handler panics before it can respond.
```
Claude reads two files, understands the mismatch, proposes a fix. Done.

---

## Key Principles

### Let Claude Read the Error Itself

Do not paraphrase. Copy and paste exact output.

**Bad:** `I'm getting some kind of type error in the handler.`

**Good:**
```
TypeError: Cannot read properties of undefined (reading 'map')
    at renderItems (src/components/ItemList.tsx:23:18)
    at HTMLUnknownElement.callCallback (node_modules/react-dom/...)
```

### Point to the File, Not the Symptom

**Bad:** `The page shows a blank screen when I click submit.`

**Good:**
```
The page shows a blank screen on submit.
Handler: src/components/Form.tsx, handleSubmit at line 45.
Network tab: POST /api/submit returns 500.
Server log: "column 'user_id' cannot be null" in routes/api.ts line 112.
```

### Include Stack Traces

Stack traces tell Claude the exact call chain. Always include them when available:

```
Error: SQLITE_CONSTRAINT: NOT NULL constraint failed: sessions.user_id
    at Database.run (/app/src/db.js:42:15)
    at SessionStore.create (/app/src/stores/session.js:28:22)
    at AuthController.login (/app/src/controllers/auth.js:67:34)
```

Claude immediately sees the constraint, the triggering function, the entry point, and the line numbers.

---

## Advanced Techniques

### Git Blame for Regressions

```
This test passed before. Run `git log --oneline -10` and
`git diff HEAD~1 -- internal/auth/` to find what changed.
```

Or provide the context yourself:

```
This broke after commit a1b2c3d (refactor auth to JWT).
The relevant diff is in src/auth/middleware.ts.
```

### Group Related Failures

```
Three tests failing after my last change, all in auth:
- TestLogin/valid_credentials: nil pointer at auth.go:47
- TestLogin/expired_token: nil pointer at auth.go:47
- TestMiddleware/no_header: expected 401, got 500

All hit the same line. Common path is getUser() in auth.go.
```

Clusters sharing a line number point to one root cause. Telling Claude this prevents it from investigating each failure independently.

### Let Claude Run the Test

Sometimes minimal is fastest:

```
Run `go test ./internal/auth/ -run TestLogin -v` and fix whatever is failing.
```

Works when: tests are fast, deterministic, and the fix is likely straightforward. Does not work for flaky tests or tests depending on external services.

---

## Prompt Patterns for Common Scenarios

**Compilation error:**
```
`make build` fails with:
internal/store/db.go:142:18: cannot use result (variable of type *sql.Rows) as type Store
The function signature is on line 138.
```

**Test failure:**
```
`npm test -- --testPathPattern=auth` output:
FAIL src/auth/__tests__/login.test.ts
    Expected: 200 / Received: 401
    at src/auth/__tests__/login.test.ts:34:25
The login handler is in src/auth/handlers.ts, line 45.
```

**Runtime crash:**
```
panic: runtime error: index out of range [0] with length 0
goroutine 1 [running]:
main.loadConfig(/home/user/app/config.go:28)
Line 28 accesses the first element of an array that might be empty.
```

**Regression:**
```
After merging PR #42, /api/users returns empty arrays. It worked before.
The PR changed src/repositories/user.ts. Run the test with
`npm test -- --testPathPattern=user.repository` and compare with
`git show HEAD~1:src/repositories/user.ts`.
```

---

## Checklist

- [ ] Exact error message (copy-pasted, not paraphrased)
- [ ] File path and line number
- [ ] Reproduction steps (command or action sequence)
- [ ] Expected vs actual behavior
- [ ] Stack trace if available
- [ ] Recent changes if this is a regression
- [ ] Related failures grouped together
