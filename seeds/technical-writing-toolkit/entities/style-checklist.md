---
title: "Style Checklist"
tags: [checklist, style, quality, review, pre-publish, entity]
content_type: entity
pinned: true
domain: technical-writing
---

# Style Checklist

Run through this checklist before publishing any documentation.

## Structure

- [ ] Title clearly describes the content
- [ ] One `#` heading per page (the title)
- [ ] Headings don't skip levels (## then ### not ## then ####)
- [ ] Content is organized for scanning (headers, lists, tables)
- [ ] Key information appears in the first paragraph, not buried at the bottom
- [ ] Page stays focused on one topic

## Clarity

- [ ] Active voice used throughout ("Run the command" not "The command should be run")
- [ ] Filler words removed ("in order to" -> "to")
- [ ] Jargon defined on first use or avoided
- [ ] Sentences under 25 words on average
- [ ] Paragraphs under 5 sentences
- [ ] Specific rather than vague ("under 50ms" not "fast")

## Examples

- [ ] Code examples are copy-pasteable and actually work
- [ ] Examples show the most common case first
- [ ] Expected output is shown after commands
- [ ] Before/after examples used where applicable

## Consistency

- [ ] Terminology is consistent (same word for same concept throughout)
- [ ] Formatting is consistent (code style, list style, heading case)
- [ ] Date format is ISO 8601 (YYYY-MM-DD)
- [ ] Units and numbers are consistent

## Accessibility

- [ ] Images have descriptive alt text
- [ ] Link text is descriptive ("see the auth guide" not "click here")
- [ ] Color is not the only way information is conveyed
- [ ] Tables have header rows
- [ ] Code blocks have language identifiers

## Internationalization

- [ ] No idioms or colloquialisms ("under the hood", "low-hanging fruit")
- [ ] No culture-specific metaphors (sports, food)
- [ ] Subjects are explicit (no ambiguous "it" or "this")
- [ ] Text in images avoided (use text-based diagrams)

## Links and References

- [ ] All links work (check with a link checker)
- [ ] Internal links use relative paths
- [ ] External links open relevant pages (not just homepages)
- [ ] "See Also" section links to 3-5 related notes

## Technical Accuracy

- [ ] Code examples tested against the current version
- [ ] Version numbers and dates are current
- [ ] Commands work on the documented platforms
- [ ] Error messages match what users actually see

## Metadata (for SAME seeds)

- [ ] YAML frontmatter includes title, tags, content_type, domain
- [ ] Tags are consistent with other notes in the vault
- [ ] content_type matches the directory (hub, research, entity, guide, decision)
- [ ] Note is under 200 lines

## See Also

- `hub/writing-style-guide.md` -- Principles behind this checklist
- `research/writing-for-international-audiences.md` -- Internationalization details
- `entities/accessibility-in-documentation.md` -- Accessibility details
- `entities/writing-tools-reference.md` -- Automated checking tools
