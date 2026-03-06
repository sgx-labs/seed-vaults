---
title: "Creating a Style Guide for Your Team"
tags: [guide, style-guide, team, consistency, vale, documentation]
content_type: guide
domain: technical-writing
---

# Creating a Style Guide for Your Team

## Prerequisites

- A team that writes documentation (even if inconsistently)
- 30 minutes to draft, 1 hour to refine with the team

## Step 1: Identify Your Inconsistencies (10 minutes)

Read through your existing docs and note where things vary:

- Do some pages use "config" and others say "configuration"?
- Are headings in Title Case or Sentence case?
- Do some docs use "you" and others use "the user"?
- Are code examples in different styles (backticks vs blocks)?
- Do some docs start with theory and others start with examples?

List the top 5 inconsistencies. These become your first style rules.

## Step 2: Write the Core Rules (20 minutes)

Start with just 5-10 rules. Here's a strong starting set:

```markdown
# Documentation Style Guide

## Voice and Tone
- Use second person ("you") for instructions
- Use active voice ("Run the command" not "The command should be run")
- Be direct. Cut filler words ("in order to" -> "to")

## Formatting
- Headings: sentence case ("How to configure auth")
- Lists: use `-` for unordered, `1.` for ordered
- Code: use backticks for inline code, triple-backtick blocks for multi-line
- Bold: for UI elements and key terms. Italic: for emphasis. Use both sparingly.

## Terminology
| Always use | Don't use |
|-----------|-----------|
| config file | configuration file, settings file |
| endpoint | route, path, URL |
| user | customer, account holder |
| API key | token, secret key, auth key |

## Structure
- Lead with the answer, then explain
- One topic per page
- Include a "See Also" section with related links
```

## Step 3: Get Team Buy-In (30 minutes)

Share the draft with your team. Two rules for the discussion:

1. **Only add rules the team agrees on.** A style guide nobody follows is useless.
2. **Start small.** You can add rules later. You can't take back rules that annoyed people.

Common debates:
- Oxford comma (pick one, doesn't matter which)
- Heading case (sentence case is simpler and works internationally)
- "We" vs "the system" (both are fine; be consistent)

## Step 4: Set Up Automated Enforcement (30 minutes)

Use Vale to enforce rules automatically:

```bash
brew install vale
```

Create `.vale.ini` in your project root:

```ini
StylesPath = .vale/styles
MinAlertLevel = warning

[*.md]
BasedOnStyles = Vale, write-good
```

Create custom rules for your terminology:

```bash
mkdir -p .vale/styles/MyProject
```

Create `.vale/styles/MyProject/Terminology.yml`:

```yaml
extends: substitution
message: "Use '%s' instead of '%s'."
level: warning
swap:
  configuration file: config file
  settings file: config file
  route: endpoint
  customer: user
```

## Step 5: Add to CI (10 minutes)

```yaml
docs-style:
  runs-on: ubuntu-latest
  steps:
    - uses: actions/checkout@v4
    - uses: errata-ai/vale-action@v2
```

Start with warnings, not errors. Let the team adjust before enforcing strictly.

## Step 6: Maintain (Ongoing)

- **Add rules when you find new inconsistencies.** One at a time.
- **Remove rules nobody cares about.** If a rule creates friction but not value, drop it.
- **Review quarterly.** Does the style guide still match how the team writes?
- **Link from CONTRIBUTING.md.** New contributors should find the style guide easily.

## What Not to Do

- **Don't write 50 rules on day one.** Nobody will read them. Start with 5-10.
- **Don't enforce without buy-in.** A style guide imposed from above gets ignored.
- **Don't be pedantic.** Style guides reduce friction, not increase it. If a rule creates more arguments than it prevents, remove it.
- **Don't copy another company's style guide verbatim.** Their terminology, audience, and culture are different.

## See Also

- `hub/writing-style-guide.md` -- The principles behind style rules
- `entities/style-checklist.md` -- Pre-publish checklist
- `entities/writing-tools-reference.md` -- Vale and other tools
- `decisions/style-guide-adoption.md` -- Decision template for adopting a style guide
