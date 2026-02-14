---
title: "Multi-File Editing Workflows"
tags: [editing, multi-file, refactoring, workflow, advanced, rename, glob-grep, atomic]
content_type: research
domain: engineering
---

# Multi-File Editing Workflows

## The Question

How do you make consistent changes across many files in a single turn, without ending up with half-applied refactors?

## The Core Principle

Describe the change conceptually, not file-by-file. Claude Code can find all the places that need updating and edit them atomically in one pass.

```
Bad:  "Open src/models/user.ts and rename User to Account.
       Then open src/api/users.ts and update the imports.
       Then open src/api/orders.ts and..."

Good: "Rename the User model to Account across the entire codebase.
       Update all imports, type references, and variable names."
```

The good prompt lets Claude use Glob and Grep to find every reference, then edit them all. The bad prompt makes you the search engine.

## How Claude Finds References

Before editing, Claude builds a complete picture of what needs to change:

```
1. Glob — find files matching patterns (*.ts, *.go, etc.)
2. Grep — search file contents for the target string or pattern
3. Read — open specific files to understand context around matches
4. Edit — apply changes to each file
```

This is the same workflow you would do manually, but Claude does it in seconds across hundreds of files.

## Workflow: The Conceptual Prompt

### Step 1: State the Change as an Outcome

```
You: "Extract the database configuration from src/config.ts into
      its own file at src/config/database.ts. Update all imports
      across the codebase."
```

### Step 2: Let Claude Search

Claude will grep for imports of the config module, read each file to understand the usage, and build a list of files to update.

### Step 3: Review the Changes

Claude edits all files in sequence. Each edit is shown to you. For large changes, Claude may summarize: "Updated 12 files to import from the new path."

### Step 4: Run Tests

```
You: "Run the tests to make sure everything still works."
```

Always verify after multi-file edits. A single missed reference can break the build.

## Patterns That Work Well

### Rename Across Codebase

```
You: "Rename the OrderService class to OrderProcessor everywhere.
      Update class name, file name, imports, and all references."
```

Claude handles the class name, the filename, every import statement, every instantiation, and every type annotation.

### Extract and Split

```
You: "The utils.ts file is 800 lines. Split it into:
      - string-utils.ts (string manipulation functions)
      - date-utils.ts (date formatting and parsing)
      - validation-utils.ts (input validation)
      Update all imports across the project."
```

### Change an Interface

```
You: "Add a 'createdAt: Date' field to the Order interface in
      shared/types.ts. Update every place that creates an Order
      object to include this field, defaulting to new Date()."
```

This is where multi-file editing shines. Claude finds the interface, then finds every object literal that satisfies it, and adds the field.

### Swap a Dependency

```
You: "Replace all uses of moment.js with date-fns across the project.
      Update imports and convert the API calls. Remove moment from
      package.json when done."
```

### Migrate a Pattern

```
You: "Convert all class-based React components in src/components/
      to functional components with hooks. Preserve the existing
      prop types and behavior."
```

## Using Plan Mode for Complex Changes

For changes that touch 10+ files or involve architectural decisions, start with plan mode:

```
You: "Plan how to migrate from REST to GraphQL for the user endpoints.
      Don't make changes yet."

Claude: [explores codebase, presents plan]
Claude: "Here's my plan:
  - 14 files affected
  - 3 new files needed (schema, resolvers, client queries)
  - 6 existing handlers to replace
  - 2 test files to update
  Shall I proceed?"

You: "Go ahead."
```

The plan gives you a chance to catch problems before Claude edits 14 files.

## Atomic Changes: Why One Prompt Beats Many

Consider adding a new field to a database model. This touches:

- The model definition
- The migration file
- The API serializer
- The API deserializer
- The frontend type definition
- The frontend form component
- The test fixtures

If you do these as seven separate prompts, each one operates without knowledge of the others. The serializer might use a different field name than the model. The test fixtures might miss the field entirely.

One prompt keeps everything consistent:

```
You: "Add an 'avatar_url' string field to the User model.
      This should be:
      - Nullable in the database (with a migration)
      - Included in the API response
      - Accepted in the PUT /users/:id endpoint
      - Displayed in the frontend profile component
      - Present in all test fixtures with a placeholder value"
```

Claude makes all changes in one pass, using consistent naming throughout.

## Tips for Large Multi-File Edits

1. **Run tests after every multi-file change.** Not after three changes -- after each one.

2. **Use git diff to review.** After Claude finishes, `git diff` shows you every change in one view. Easier than reviewing file-by-file in the conversation.

3. **Commit before starting.** If the multi-file edit goes wrong, `git checkout .` gets you back. Always have a clean baseline.

4. **Be explicit about naming conventions.** "Use camelCase for the new variables" prevents inconsistency.

5. **Scope the change.** "Update imports in the src/api/ directory" is better than "update imports everywhere" if you know only that directory is affected.

6. **Provide the pattern when one exists.** "Follow the same pattern as the existing PUT endpoint in users.ts" gives Claude a concrete template.

7. **Ask Claude to list affected files first.** "Before making changes, list all files that reference OrderService." This lets you verify the scope before any edits happen.

## When Multi-File Editing Gets Risky

- **100+ files**: Consider breaking into batches by directory or module.
- **Generated code**: Claude may edit files that get overwritten by a code generator. Ask Claude to check for generated file headers.
- **Circular dependencies**: Renaming across deeply tangled modules can introduce import cycles. Plan mode helps here.
- **Concurrent work**: If teammates are editing the same files, large multi-file changes create merge conflicts. Coordinate timing.
