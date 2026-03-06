---
title: "Reviewing Someone Else's Documentation"
tags: [guide, review, feedback, pull-request, quality, documentation]
content_type: guide
domain: technical-writing
---

# Reviewing Someone Else's Documentation

## The Mindset

Reviewing documentation is not the same as reviewing code. Code reviews check for correctness. Documentation reviews check for *clarity and usefulness to the reader*.

When reviewing docs, you are the reader's advocate. Ask: "Would this help me if I didn't already know this?"

## The Review Checklist

### 1. Can I Understand It?

Read the document as if you don't know the topic. Note every moment of confusion:

- "What does this term mean?"
- "Why would I do this?"
- "What happens if this step fails?"
- "Where does this output go?"

Every confusion point is a documentation bug.

### 2. Is It Accurate?

- Do the code examples work? (Copy-paste and run them if possible.)
- Are the commands correct for the documented platform?
- Do the described behaviors match the current codebase?
- Are version numbers and links current?

### 3. Is It Structured for Scanning?

- Can I understand the page from headings alone?
- Is key information in the first paragraph, not buried?
- Are steps numbered? Are lists used instead of long paragraphs?
- Are code blocks used for anything that might be copied?

### 4. Is It Complete?

- Are prerequisites listed?
- Is the expected outcome described for each step?
- Are error cases covered ("if this doesn't work...")?
- Is there a "See Also" section for related topics?

### 5. Is It Consistent?

- Does it match the style guide (if one exists)?
- Is terminology consistent with other docs?
- Is the formatting consistent (heading levels, list styles)?

## How to Give Feedback

### Be Specific

**Don't write this:**

> "This section is confusing."

**Write this instead:**

> "In step 3, 'configure the service' — which service? The API server or the background worker? And what does 'configure' mean here — edit a file, run a command, or change an environment variable?"

### Suggest, Don't Rewrite

**Don't write this:**

> "Change this paragraph to: [your rewritten version]."

**Write this instead:**

> "This paragraph tries to cover three things: what the feature does, how to configure it, and when to use it. Consider splitting into separate sections for each."

### Distinguish Must-Fix from Nice-to-Have

Use a consistent system:

- **Blocking:** "This example has a typo in the command that will cause an error."
- **Suggestion:** "Consider adding expected output after step 4 so readers know if it worked."
- **Nit:** "Minor style: 'utilize' -> 'use'."

### Praise Good Work

If something is well-written, say so. "The before/after example in the error handling section is really clear" encourages more good writing.

## Common Review Mistakes

- **Reviewing only for grammar.** Grammar matters, but clarity and accuracy matter more.
- **Assuming your knowledge.** You understand the system. The reader might not.
- **Rewriting instead of guiding.** Help the author improve their writing, don't replace it.
- **Ignoring structure.** A well-written paragraph in the wrong place is still a problem.
- **Skipping code examples.** Untested examples are the most common source of documentation bugs.

## See Also

- `hub/writing-style-guide.md` -- What good style looks like
- `entities/style-checklist.md` -- Systematic review checklist
- `hub/code-comments-philosophy.md` -- Reviewing code comments alongside code
- `research/documentation-as-code.md` -- Review process in PRs
