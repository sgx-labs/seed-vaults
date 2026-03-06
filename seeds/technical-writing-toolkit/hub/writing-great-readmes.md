---
title: "Writing Great READMEs"
tags: [readme, structure, first-impression, onboarding, documentation]
content_type: hub
domain: technical-writing
---

# Writing Great READMEs

## Why It Matters

Your README is the front door to your project. Most people decide whether to use your software within 30 seconds of reading it. A great README answers three questions immediately: What is this? Why should I care? How do I start?

## The Minimum Viable README

Every README needs these sections, in this order:

1. **Title and one-line description** -- what it is, in plain English
2. **Install / quickstart** -- from zero to running in under 60 seconds
3. **Usage example** -- show the most common use case with real code
4. **License** -- so people know if they can use it

That's it for the MVP. Everything else is optional until your project has users.

## The Complete README

For established projects, expand to include:

1. Title and description
2. Badges (build status, version, license -- keep it to 3-4 max)
3. Install / quickstart
4. Usage examples (2-3 common scenarios)
5. Configuration (if applicable)
6. API overview (link to full docs)
7. Contributing (link to CONTRIBUTING.md)
8. License

## Before and After

**Don't write this:**

```markdown
# MyTool

MyTool is a cloud-native, enterprise-grade, AI-powered solution
for synergizing cross-functional team dynamics through innovative
paradigm-shifting methodologies.

## Installation

See INSTALL.md for details.
```

**Write this instead:**

```markdown
# mytool

CLI for converting CSV files to JSON. Handles large files,
nested headers, and custom delimiters.

## Install

brew install mytool

## Quick start

mytool convert data.csv              # outputs data.json
mytool convert data.csv --pretty     # formatted output
mytool convert *.csv --output ./json # batch convert
```

## What to Skip

- **Badges you don't need.** If you have 8 badges, you have 6 too many.
- **Lengthy history.** Nobody reads "In 2019, we started this project because..." Put history in a separate doc.
- **Implementation details.** The README sells; the docs explain.
- **Screenshots of text.** Use actual text. Screenshots aren't searchable, accessible, or copy-pasteable.
- **"Table of Contents" for short READMEs.** If your README needs a TOC, it might be too long.

## Common Mistakes

- **No install instructions.** "Clone the repo" is not an install instruction.
- **Outdated examples.** Examples that don't work are worse than no examples.
- **Assuming context.** Not everyone knows what your framework or acronym means.
- **Wall of text.** Use headers, lists, and code blocks. Make it scannable.
- **Placeholder content.** "TODO: add description" left in a published README.

## The README Litmus Test

Can someone who has never seen your project:
1. Understand what it does in 10 seconds?
2. Install it in under 2 minutes?
3. Run a basic example in under 5 minutes?

If not, your README needs work.

## See Also

- `hub/onboarding-documentation.md` -- Beyond the README: full onboarding
- `hub/writing-style-guide.md` -- Style principles for all documentation
- `hub/diataxis-framework.md` -- Where READMEs fit in documentation types
- `entities/common-documentation-templates.md` -- README template
- `guides/starting-documentation-from-zero.md` -- Building docs when you have none
