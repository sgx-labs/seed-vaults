---
title: "How Do I Handle My First External Contributor PR?"
tags: [contributors, pull-requests, community, onboarding, code-review, first-pr]
content_type: research
domain: engineering
---

# How Do I Handle My First External Contributor PR?

## The Question

Someone you have never met just opened a PR on your project. Now what? How you handle this moment determines whether they become a long-term contributor or never come back.

## The 24-Hour Rule

Respond within 24 hours. You do not need to merge within 24 hours — just acknowledge the PR. A quick "Thanks for this! I will review it this week." is enough. Silence is the contributor killer.

## Review Checklist

### Before You Read the Code

1. **Thank them** — "Thank you for contributing!" Every time. No exceptions.
2. **Check if you have a CONTRIBUTING.md** — If not, create one now. They found you without it.
3. **Read their PR description** — What problem are they solving?

### During Review

1. **Be kind and specific** — "Could you rename this to `processEvent`? We use verb-noun for functions." Not: "Wrong naming."
2. **Explain the why** — "We avoid this pattern because X." Not just "Please change this."
3. **Separate blocking from non-blocking** — Use "nit:" prefix for style suggestions that should not block the merge.
4. **Do not rewrite their PR** — If it needs major changes, discuss the approach first. Offer to pair or give specific guidance.
5. **Lower your standards slightly** — A first PR does not need to be perfect. You can always improve it later.

### Merge Decision

- **Good enough to merge as-is** — Merge, thank them, celebrate publicly.
- **Needs minor changes** — Request specific changes with examples.
- **Wrong approach** — Explain why, suggest the right approach, offer help.
- **Out of scope** — "This is interesting but does not align with our current direction. Here is why..." Close kindly.

## After Merging

1. Thank them in the PR comments.
2. Add them to your README's contributors section (or use the All Contributors bot).
3. Mention them in the next release notes.
4. If they were great, invite them to tackle another issue.

## Response Templates

### First Response
```
Thanks for opening this PR! I appreciate you taking the time to contribute.
I will review this in detail [today/this week]. In the meantime, CI should
run the tests automatically.
```

### Requesting Changes
```
This is a great start! I have a few suggestions to align with our conventions:

1. [Specific change with example]
2. [Specific change with example]

Let me know if any of this is unclear — happy to explain our reasoning.
```

### Closing Without Merging
```
Thank you for the effort here. I am going to close this PR because
[specific reason]. This is not a reflection on the quality of your work —
[explanation of project direction].

If you are interested in contributing, [alternative suggestion or issue link].
```

## Common Mistakes

- Ignoring the PR for days/weeks (contributor feels disrespected)
- Being overly critical of style issues (drives people away)
- Merging without any comment (contributor gets no feedback loop)
- Rewriting their code yourself (they learn nothing, feel dismissed)

## Related

- `hub/community.md` — Community building overview
- `research/community/managing-issues-prs.md` — Scaling issue management
- `templates/contributing-template.md` — CONTRIBUTING.md template
