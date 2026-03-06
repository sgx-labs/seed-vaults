---
title: "Markdown Syntax Reference"
tags: [markdown, syntax, gfm, reference, formatting, entity]
content_type: entity
pinned: true
domain: technical-writing
---

# Markdown Syntax Reference

## Text Formatting

| Syntax | Renders as |
|--------|-----------|
| `**bold**` | **bold** |
| `*italic*` | *italic* |
| `***bold italic***` | ***bold italic*** |
| `~~strikethrough~~` | ~~strikethrough~~ (GFM) |
| `` `inline code` `` | `inline code` |

## Headings

```markdown
# Heading 1
## Heading 2
### Heading 3
#### Heading 4
```

One `#` per document. Don't skip levels.

## Links

```markdown
[Link text](https://example.com)
[Link with title](https://example.com "Hover text")
[Relative link](../docs/guide.md)
[Reference link][ref-id]

[ref-id]: https://example.com
```

## Images

```markdown
![Alt text describing the image](./image.png)
![Logo](https://example.com/logo.png "Optional title")
```

Always include alt text for accessibility.

## Lists

```markdown
Unordered:
- Item one
- Item two
  - Nested item

Ordered:
1. First step
2. Second step
3. Third step

Task list (GFM):
- [x] Completed
- [ ] Not done
```

## Code

Inline: surround with single backticks.

Block: use triple backticks with language identifier:

````markdown
```python
def hello():
    print("hello")
```
````

Supported languages: `python`, `javascript`, `go`, `rust`, `bash`, `json`, `yaml`, `toml`, `sql`, `markdown`, `diff`, and many more.

## Tables (GFM)

```markdown
| Left | Center | Right |
|:-----|:------:|------:|
| a    | b      | c     |
| d    | e      | f     |
```

Alignment: `:---` left, `:---:` center, `---:` right.

## Blockquotes

```markdown
> Single line quote

> Multi-line quote
> continues on next line

> Nested
> > quote
```

## Horizontal Rule

```markdown
---
```

Use sparingly. Headings are usually better section dividers.

## Alerts (GFM)

```markdown
> [!NOTE]
> Useful information.

> [!TIP]
> Helpful advice.

> [!IMPORTANT]
> Critical information.

> [!WARNING]
> Potential issues.

> [!CAUTION]
> Dangerous actions.
```

## Collapsible Sections (HTML in Markdown)

```markdown
<details>
<summary>Click to expand</summary>

Hidden content here. Leave a blank line after `<summary>`.

</details>
```

## Footnotes (GFM)

```markdown
Here is a statement[^1].

[^1]: This is the footnote text.
```

## Escaping

Use backslash to escape Markdown characters:

```markdown
\* not italic \*
\# not a heading
\[not a link\]
```

## YAML Frontmatter

```markdown
---
title: "Page Title"
tags: [tag1, tag2]
date: 2025-03-15
---

Content starts after the closing `---`.
```

## See Also

- `research/markdown-best-practices.md` -- Writing guidelines
- `entities/mermaid-diagram-reference.md` -- Diagrams in Markdown
- `entities/writing-tools-reference.md` -- Markdown linting tools
