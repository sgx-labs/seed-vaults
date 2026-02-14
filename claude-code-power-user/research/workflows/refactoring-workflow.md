---
title: "Safe Refactoring with Claude Code"
tags: [refactoring, plan-mode, testing, workflow, code-quality]
content_type: research
domain: engineering
---

# Safe Refactoring with Claude Code

## The Golden Rule

Refactoring with Claude Code follows one rule: **prove nothing broke at every step.** Claude can rename, move, extract, and restructure code across dozens of files in seconds. That speed is dangerous without verification. The workflow is: plan, change, test, repeat.

## Always Start with Plan Mode

Refactoring touches many files and has cascading effects. Never let Claude start refactoring without a plan:

```
You: "I want to split the User model into UserProfile and UserAuth.
      Use plan mode to analyze the impact first."

Claude: [enters plan mode]
Claude: [greps for all User references, reads each file]
Claude: "Impact analysis:
  - 23 files reference the User type
  - 8 files only use profile fields (name, email, avatar)
  - 12 files only use auth fields (password_hash, token, role)
  - 3 files use both

  Plan:
  1. Create UserProfile and UserAuth types in models/user.go
  2. Add a UserFull type embedding both for the 3 mixed-use files
  3. Update the 8 profile-only files
  4. Update the 12 auth-only files
  5. Update the 3 mixed-use files
  6. Update all tests
  7. Run full test suite

  Estimated files changed: 26
  Risk: medium — some files may have implicit dependencies on
  fields we move."
```

## Run Tests Before and After

### Before: Establish a Baseline

```
You: "Run the full test suite before we start. I want to know
      our baseline."

Claude: [runs tests]
Claude: "All 147 tests pass. 3 are skipped (integration tests).
        Starting from a clean baseline."
```

If tests are already failing, fix them first. Refactoring on top of broken tests makes it impossible to tell if you introduced new problems.

### After Each Step: Verify

```
You: "Run tests after this change."

Claude: [runs tests]
Claude: "2 tests failing in handlers/profile_test.go — the test
        still references User.PasswordHash which moved to UserAuth."
You: "Fix those tests."
Claude: [fixes, re-runs]
Claude: "All 147 tests pass."
```

## Small Incremental Changes

### Break Big Refactors into Steps

Do not try to refactor everything in one pass. Break it into commits:

```
Step 1: Create new types (don't change anything else)
Step 2: Update one package to use new types
Step 3: Run tests, commit
Step 4: Update next package
Step 5: Run tests, commit
... repeat ...
Step N: Remove old type, final test run, commit
```

This gives you a clear rollback point at each step. If step 7 breaks something, you only need to undo step 7, not the entire refactor.

### Commit After Each Successful Step

```
You: "Tests pass. Commit this step: 'Migrate handlers package
      to UserProfile type'."

# Continue with the next step...
```

## Common Refactoring Patterns

### Extract Function

Pull a block of code into its own function:

```
You: "Extract the validation logic from CreateOrder (lines 45-78)
      into a separate validateOrder function."
```

Claude will:
1. Read the function
2. Identify the inputs and outputs of the extracted block
3. Create the new function with the right signature
4. Replace the original block with a call to the new function
5. Update tests if needed

### Rename Symbol

Rename a function, type, variable, or constant across the codebase:

```
You: "Rename the function ProcessData to ProcessTransaction
      everywhere in the codebase."
```

Claude will:
1. Find all references (definition, calls, tests, comments)
2. Rename all of them
3. Update imports if the name affects package-level visibility

### Move to New File

Split a large file into smaller, focused files:

```
You: "Move all the validation functions from handlers.go into
      a new file called validation.go in the same package."
```

Claude will:
1. Identify which functions to move
2. Determine which imports the moved functions need
3. Create the new file with the right package declaration and imports
4. Remove the functions from the original file
5. Clean up imports in the original file

### Change Interface

Modify a function signature or interface and update all callers:

```
You: "Add a context.Context parameter as the first argument to
      all repository methods. Update all callers."
```

Claude will:
1. Find the interface definition
2. Update each method signature
3. Find all implementations and update them
4. Find all call sites and update them
5. Add context imports where needed

### Replace Dependency

Swap one library for another:

```
You: "Replace all uses of pkg/errors with the standard library
      errors and fmt.Errorf. Use %w for wrapping."
```

Claude will:
1. Find all imports of the old package
2. Replace each usage pattern with the standard library equivalent
3. Update imports
4. Remove the old dependency from go.mod/package.json

## Multi-File Rename: How Claude Handles It

When a rename affects many files, Claude processes them methodically:

```
You: "Rename the package 'utils' to 'helpers' and update all imports."

Claude's approach:
1. Rename the directory: utils/ -> helpers/
2. Update the package declaration in every file in the directory
3. Find all import statements referencing "project/utils"
4. Replace with "project/helpers" in every file
5. Run tests to verify
```

For large renames (50+ files), Claude may use the Task tool to run subagents in parallel, updating files concurrently.

## Refactoring Checklist

Before starting:

- [ ] All tests pass (establish baseline)
- [ ] Working tree is clean (no uncommitted changes)
- [ ] You are on a feature branch (not main)
- [ ] You have a plan (used plan mode)

During:

- [ ] One logical change per step
- [ ] Tests run after each step
- [ ] Commit after each passing step
- [ ] Review diffs before committing

After:

- [ ] Full test suite passes
- [ ] No dead code left behind (unused imports, functions)
- [ ] No TODO comments from the refactoring process
- [ ] Documentation updated if public APIs changed

## When Refactoring Goes Wrong

### Tests Fail After a Change

```
You: "Tests are failing after the rename. Show me the failures."

Claude: [shows test output]

You: "Fix the test failures and re-run."
```

### Too Many Files Changed

If a refactoring plan shows 50+ files changing, consider:

1. **Is the scope too broad?** Break it into smaller refactors.
2. **Is there a simpler approach?** Sometimes a type alias or adapter is better than a full rewrite.
3. **Should this be a separate branch?** Large refactors deserve their own PR.

### You Want to Undo

If you committed at each step (as recommended), rolling back is straightforward:

```
You: "The last refactoring step broke things. Revert the last commit."
```

If you did not commit incrementally:

```
You: "Show me git stash or the diff so I can decide what to keep."
```

## Tips

1. **Always use a feature branch.** Never refactor on main.
2. **Plan mode is not optional for refactoring.** The cost of planning is tiny compared to the cost of undoing a bad refactor.
3. **Commit after every green test run.** Small commits are cheap insurance.
4. **Let Claude find the references.** It greps more thoroughly than you will manually.
5. **Review every diff.** Claude might rename something in a comment or string literal that should have stayed the same.
6. **Run the linter too.** Tests passing does not mean the code is clean. Ask Claude to run your linter after refactoring.
