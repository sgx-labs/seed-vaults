---
title: "Diataxis Framework Reference"
tags: [diataxis, reference, tutorial, how-to, explanation, documentation-types, entity]
content_type: entity
domain: technical-writing
---

# Diataxis Framework Reference

## The Four Modes

|  | Practical (doing) | Theoretical (understanding) |
|---|---|---|
| **Learning** | Tutorial | Explanation |
| **Working** | How-to Guide | Reference |

## Quick Identification

| Mode | Reader asks | Writer provides | Structure |
|------|-----------|----------------|-----------|
| Tutorial | "Teach me" | Learning experience | Guided lesson with steps |
| How-to | "Help me do X" | Problem solution | Task-focused steps |
| Reference | "What is X?" | Facts and specs | Organized for lookup |
| Explanation | "Why is it this way?" | Context and reasoning | Discursive prose |

## Characteristics

### Tutorial
- Author leads, reader follows
- Learning-oriented, not task-oriented
- Every step produces a visible result
- Minimal tangents -- stay on the path
- Test: Can a beginner complete it unassisted?

### How-to Guide
- Reader comes with a goal
- Assumes existing knowledge
- Focused on a single task
- Flexible -- reader adapts to their situation
- Test: Does it solve a real problem?

### Reference
- Complete and accurate
- Organized by structure (alphabetical, by category)
- Consistent format across all entries
- Terse -- no persuasion, no stories
- Test: Can someone look up any detail quickly?

### Explanation
- No steps to follow
- Explores context, history, tradeoffs
- Multiple perspectives considered
- Can be read independently of any task
- Test: Does the reader understand *why*?

## Anti-Patterns

| Problem | Cause |
|---------|-------|
| Tutorial that stops to explain theory | Mixing tutorial + explanation |
| Reference page with long introductions | Mixing reference + explanation |
| How-to guide that assumes nothing | Mixing how-to + tutorial |
| Explanation with step-by-step instructions | Mixing explanation + how-to |

## Mapping to Seed Directories

| Diataxis | Seed directory | Content type |
|----------|---------------|-------------|
| Tutorial | `guides/` | Beginner walkthroughs |
| How-to | `guides/` | Task-specific guides |
| Reference | `entities/` | Lookups, syntax, templates |
| Explanation | `hub/`, `research/` | Context, tradeoffs, philosophy |

## Source

Diataxis was created by Daniele Procida. Full framework: [diataxis.fr](https://diataxis.fr)

## See Also

- `hub/diataxis-framework.md` -- Detailed explanation of each mode
- `research/writing-for-different-audiences.md` -- Audience considerations
