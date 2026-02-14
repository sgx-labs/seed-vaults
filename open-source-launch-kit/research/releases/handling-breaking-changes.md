---
title: "How Do I Handle Breaking Changes Without Losing Users?"
tags: [breaking-changes, migration, versioning, communication, trust, deprecation, semver]
content_type: research
domain: engineering
---

# How Do I Handle Breaking Changes Without Losing Users?

## The Question

Sometimes you need to break backward compatibility. How do you do it without losing the users who depend on your project?

## The Deprecation Cycle

Never remove something without warning. Follow this cycle:

### Version N (Current)
Mark the old behavior as deprecated. Print a warning.

```
Warning: --output is deprecated. Use --format instead.
         --output will be removed in v3.0.0.
```

### Version N+1 (Minor Release)
Keep both the old and new behavior working. Louder warnings.

### Version N+2 (Major Release)
Remove the deprecated behavior. Document the migration.

## Communication Checklist

For every breaking change, provide:

1. **What changed** — The specific behavior that is different
2. **Why it changed** — The reason (better UX, performance, consistency)
3. **How to migrate** — Step-by-step instructions with before/after examples
4. **When it will happen** — Timeline (announce at least one minor version ahead)

## Migration Guide Template

Include this in your changelog and release notes:

```markdown
## Migration Guide: v2 to v3

### Breaking: `--output` renamed to `--format`

**Before (v2):**
your-tool search "query" --output json

**After (v3):**
your-tool search "query" --format json

**Why:** The `--format` flag is consistent with other commands
that already use `--format`.

**Automated migration:**
sed -i 's/--output/--format/g' your-scripts/*.sh
```

## Minimizing Breakage

### Strategies to Avoid Breaking Changes

1. **Add, do not change** — New flags and functions do not break anyone
2. **Default to old behavior** — New features can be opt-in initially
3. **Accept both** — Support old and new syntax during transition period
4. **Environment variable overrides** — Let users opt into new behavior early

### When Breaking Changes Are Justified

- Security fixes that require behavioral changes
- Fixing a design mistake that causes ongoing confusion
- Removing unmaintained dependencies
- Major API redesign that significantly improves usability

### When They Are NOT Justified

- Renaming things for aesthetic reasons
- Changes that could be additive instead
- Breaking for consistency when few users are affected
- "We should have done it this way from the start"

## Conventional Commits for Breaking Changes

Signal breaking changes in your commit messages:

```
feat!: rename --output flag to --format

BREAKING CHANGE: The --output flag has been renamed to --format.
Update any scripts or automation that use --output.
```

The `!` after the type and the `BREAKING CHANGE:` footer both signal a major version bump.

## Related

- `research/releases/semantic-versioning.md` — When to bump MAJOR
- `research/releases/writing-changelogs.md` — Documenting changes
- `research/releases/release-notes.md` — Communicating releases
