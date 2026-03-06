---
title: "Markdown Best Practices"
tags: [markdown, portable, accessible, formatting, gfm, documentation]
content_type: research
domain: technical-writing
---

# Markdown Best Practices

## Why Markdown

Markdown is the lingua franca of developer documentation. It's readable as plain text, renders on every platform, diffs cleanly in version control, and has zero vendor lock-in. When in doubt, use Markdown.

## Portable Markdown

Not all Markdown is created equal. Different renderers support different features. Write for portability:

### Safe Everywhere

These work in standard Markdown, GitHub, GitLab, Docusaurus, MkDocs, and most renderers:

- Headings (`# ## ###`)
- Bold, italic (`**bold**`, `*italic*`)
- Unordered and ordered lists
- Code blocks (triple backtick with language hint)
- Links (`[text](url)`)
- Images (`![alt](url)`)
- Blockquotes (`>`)
- Horizontal rules (`---`)

### GitHub Flavored Markdown (GFM) Only

These work on GitHub and most modern renderers, but not all:

- Tables (`| col | col |`)
- Task lists (`- [x] done`)
- Strikethrough (`~~text~~`)
- Footnotes (`[^1]`)
- Alerts (`> [!NOTE]`)
- Mermaid diagrams (` ```mermaid `)
- Auto-linked URLs

### Avoid for Portability

- HTML tags (some renderers strip them)
- Custom CSS or inline styles
- Platform-specific extensions (Notion callouts, Confluence macros)
- Absolute image paths (use relative paths or URLs)

## Formatting Principles

### Headings

- One `#` per file (the title).
- Don't skip levels (`##` to `####`). Go in order.
- Keep headings short and descriptive.
- Use sentence case ("How to configure auth"), not title case ("How To Configure Auth").

### Lists

- Use `-` for unordered lists (consistent across renderers).
- Use `1.` for all items in ordered lists (Markdown auto-numbers).
- Indent nested lists with 2 or 4 spaces (pick one, be consistent).
- Keep list items parallel in grammar ("Add X", "Configure Y", "Run Z").

### Code Blocks

Always specify the language for syntax highlighting:

````markdown
```python
def hello():
    print("hello")
```
````

Use inline code (`backticks`) for:
- File names: `config.toml`
- CLI commands: `make test`
- Variable names: `user_id`
- Short code fragments: `if err != nil`

### Links

- Use descriptive link text, not "click here."
- Prefer relative links for internal references: `../hub/readme.md`
- Check links in CI (they break over time).

**Don't write this:**

```markdown
For more information, click [here](link).
```

**Write this instead:**

```markdown
See the [authentication guide](../hub/authentication.md) for details.
```

### Tables

Use tables for structured comparisons. Don't use tables for layouts.

```markdown
| Feature | Free | Pro |
|---------|------|-----|
| Users   | 5    | 100 |
| Storage | 1 GB | 50 GB |
```

Keep tables narrow. Wide tables break on mobile.

## Accessibility

- **Alt text on images.** `![Diagram showing data flow between services](./arch.png)` not `![](./arch.png)`.
- **Descriptive links.** Screen readers read link text out of context.
- **Semantic headings.** Don't use bold text as a heading. Use actual heading syntax.
- **Code blocks over screenshots.** Text is accessible; screenshots of text are not.

## Linting

Use tools to enforce consistency:

- **markdownlint** -- catches formatting issues, inconsistent heading levels, trailing whitespace
- **Vale** -- prose linting for style, jargon, and readability
- **alex** -- catches insensitive or inconsiderate language
- **lychee** -- checks for broken links

See `entities/writing-tools-reference.md` for setup details.

## See Also

- `entities/markdown-syntax-reference.md` -- Complete syntax reference
- `entities/writing-tools-reference.md` -- Linting and checking tools
- `research/documentation-as-code.md` -- Markdown in a docs-as-code workflow
- `entities/accessibility-in-documentation.md` -- Accessibility details
- `hub/writing-style-guide.md` -- Writing style within Markdown
