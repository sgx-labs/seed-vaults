---
title: "Writing Tools Reference"
tags: [tools, vale, markdownlint, alex, write-good, linting, reference, entity]
content_type: entity
domain: technical-writing
---

# Writing Tools Reference

## Markdown Linting

### markdownlint / markdownlint-cli2

Catches formatting inconsistencies in Markdown files.

**Install:** `npm install -g markdownlint-cli2`

**Run:** `markdownlint-cli2 "docs/**/*.md"`

**Config (`.markdownlint.json`):**
```json
{
  "default": true,
  "MD013": false,
  "MD033": false,
  "MD041": false
}
```

**Key rules:**
- MD001: Heading increment (don't skip levels)
- MD009: No trailing spaces
- MD012: No multiple blank lines
- MD013: Line length (often disabled)
- MD024: No duplicate headings
- MD033: No inline HTML (often disabled for details/summary)

**CI integration:**
```yaml
- uses: DavidAnson/markdownlint-cli2-action@v16
```

## Prose Linting

### Vale

Configurable prose linter. Enforces style guides (Microsoft, Google, write-good, or custom).

**Install:** `brew install vale`

**Run:** `vale docs/`

**Config (`.vale.ini`):**
```ini
StylesPath = .vale/styles
MinAlertLevel = suggestion

[*.md]
BasedOnStyles = Vale, write-good
```

**Built-in styles:** Microsoft, Google, write-good, Joblint, proselint

**What it catches:**
- Passive voice
- Weasel words ("very", "really", "quite")
- Complex words (use "use" not "utilize")
- Readability scores
- Custom terminology enforcement

### alex

Catches insensitive, inconsiderate language.

**Install:** `npm install -g alex`

**Run:** `alex docs/`

**What it catches:**
- Gendered language ("guys" -> "everyone")
- Ableist language ("crazy" -> "unexpected")
- Racial or culturally insensitive terms
- Profanity

### write-good

Simple prose checker. Good for quick feedback.

**Install:** `npm install -g write-good`

**Run:** `write-good docs/getting-started.md`

**What it catches:**
- Passive voice
- Weasel words
- "There is/are" constructions
- Adverbs ("quickly", "easily")
- Overly complex sentences

## Link Checking

### lychee

Fast link checker written in Rust.

**Install:** `brew install lychee`

**Run:** `lychee docs/ README.md`

**CI integration:**
```yaml
- uses: lycheeverse/lychee-action@v1
  with:
    args: "docs/ README.md --exclude linkedin.com"
```

**Features:**
- Checks internal and external links
- Handles rate limiting
- Exclude patterns for flaky URLs
- GitHub-aware (handles github.com rate limits)

### markdown-link-check

Node-based alternative.

**Install:** `npm install -g markdown-link-check`

**Run:** `find docs -name '*.md' -exec markdown-link-check {} \;`

## Spell Checking

### cspell

Code-aware spell checker.

**Install:** `npm install -g cspell`

**Run:** `cspell docs/**/*.md`

**Config (`cspell.json`):**
```json
{
  "words": ["docusaurus", "frontmatter", "runbook", "seedvault"],
  "ignorePaths": ["node_modules", "_archive"]
}
```

**CI integration:**
```yaml
- uses: streetsidesoftware/cspell-action@v6
```

## Recommended Stack

| Team size | Tools |
|-----------|-------|
| Solo / small team | markdownlint + lychee |
| Growing team | + Vale (write-good style) + cspell |
| Large team / public docs | + alex + custom Vale rules + CI enforcement |

Start with markdownlint and link checking. Add prose linting when consistency matters.

## See Also

- `research/documentation-as-code.md` -- CI integration for these tools
- `entities/style-checklist.md` -- What these tools check automatically
- `research/markdown-best-practices.md` -- Formatting standards
- `guides/creating-a-style-guide.md` -- Setting up Vale for your team
