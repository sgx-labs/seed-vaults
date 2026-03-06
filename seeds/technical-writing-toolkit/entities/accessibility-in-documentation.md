---
title: "Accessibility in Documentation"
tags: [accessibility, a11y, inclusive, screen-reader, alt-text, entity]
content_type: entity
domain: technical-writing
---

# Accessibility in Documentation

## Why It Matters

Accessible documentation serves everyone, including developers using screen readers, people with low vision, those who can't distinguish colors, and anyone on a slow connection where images don't load.

## Images

### Alt Text

Every image needs descriptive alt text that conveys the same information as the image.

**Don't write this:**

```markdown
![](diagram.png)
![diagram](diagram.png)
![image](screenshot.png)
```

**Write this instead:**

```markdown
![Architecture diagram showing client, API server, database, and cache connections](diagram.png)
![Terminal output showing three passing tests and one failure](screenshot.png)
```

**Rules for alt text:**
- Describe what the image shows, not what it is
- If the image contains text, include that text in the alt
- For decorative images, use empty alt text: `![](decorative.png)` -- but question whether the image is needed at all
- Keep alt text under 125 characters

### Prefer Text Over Images

- Use text-based diagrams (Mermaid, ASCII) instead of image diagrams when possible
- Don't screenshot code -- use code blocks
- Don't screenshot terminal output -- copy the text
- If you must use an image, provide a text equivalent nearby

## Headings

Use semantic heading levels, not visual formatting.

**Don't do this:**

```markdown
**This looks like a heading but isn't**

It's just bold text. Screen readers won't navigate to it.
```

**Do this instead:**

```markdown
## This Is a Real Heading

Screen readers can jump to it. Outline views display it.
```

## Links

Screen readers often list all links on a page out of context. Link text must be meaningful on its own.

| Inaccessible | Accessible |
|-------------|-----------|
| "Click here" | "See the [installation guide](link)" |
| "Read more" | "Read the [API authentication docs](link)" |
| "Link" | "[Rate limiting configuration](link)" |
| Raw URL | "[SAME documentation](https://...)" |

## Color

Don't use color as the only way to convey information.

**Don't write this:**

> Required fields are shown in red.

**Write this instead:**

> Required fields are marked with an asterisk (*).

## Tables

- Always include a header row
- Keep tables simple -- avoid merged cells
- Provide a caption or preceding paragraph explaining what the table shows
- For complex data, consider a list format instead

## Code Blocks

- Always specify the language for syntax highlighting
- Use descriptive comments within code examples
- Ensure code blocks have sufficient contrast in the rendered output

## Language

- Use plain language (see `research/writing-for-international-audiences.md`)
- Define abbreviations on first use: "ADR (Architecture Decision Record)"
- Avoid directional language: "see above" or "the section below" -- use section names instead

## Testing

- **Screen reader test:** Read your docs with a screen reader (VoiceOver on Mac, NVDA on Windows). Are they usable?
- **Keyboard test:** Can you navigate entirely with keyboard?
- **Zoom test:** Does the content make sense at 200% zoom?
- **No-image test:** Disable images. Does the content still work?

## See Also

- `research/markdown-best-practices.md` -- Accessible Markdown
- `entities/style-checklist.md` -- Accessibility items on the checklist
- `research/writing-for-international-audiences.md` -- Overlapping accessibility concerns
- `hub/writing-style-guide.md` -- Clarity helps accessibility
