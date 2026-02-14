---
title: "When Should I Archive or Hand Off My Project?"
tags: [archive, handoff, maintenance, sustainability, end-of-life, transfer, deprecation]
content_type: research
domain: engineering
---

# When Should I Archive or Hand Off My Project?

## The Question

Not every project needs to live forever. When is it okay to stop maintaining a project, and how do you do it responsibly?

## Signs It Is Time

### You Should Consider Archiving When:

- You have not worked on it in 6+ months and have no plans to
- The problem it solved no longer exists (technology moved on)
- A better alternative has emerged and you would recommend it
- Maintaining it is actively harming your wellbeing
- You have moved on professionally and lost context

### You Should Consider Handing Off When:

- The project has active users but you cannot maintain it
- A contributor has been doing most of the work already
- You want it to live but cannot be the one to keep it alive
- A company or organization wants to adopt it

## How to Archive Responsibly

### Step 1: Announce Your Plans

Post in all communication channels (GitHub, Discord, README):

```markdown
## Project Status: Archived

This project is no longer actively maintained as of [date].
It will remain available but will not receive updates or
bug fixes.

**Why:** [Honest, brief explanation]

**Alternatives:** Consider using [alternative-1] or [alternative-2]
for similar functionality.

**Forks welcome:** If someone wants to continue development,
please fork this repository. I am happy to link to active forks.
```

### Step 2: Update the README

Add a clear status notice at the very top of the README:

```markdown
> **Note:** This project is archived and no longer maintained.
> See [alternative] for an actively maintained option.
```

### Step 3: Archive on GitHub

1. Settings > Danger Zone > Archive this repository
2. This makes the repo read-only (no new issues, PRs, or changes)
3. The repo remains visible and cloneable

### Step 4: Redirect Users

- Close all open issues with a kind explanation
- Disable GitHub Discussions
- Update npm/PyPI package description to note archived status

## How to Hand Off Responsibly

### Finding a New Maintainer

1. **Ask active contributors first** — They already know the codebase
2. **Post in the project's communication channels** — Someone may volunteer
3. **Check forks** — Someone actively forking may want to take over
4. **Post on maintainer forums** — Some communities match projects with maintainers

### Transfer Process

1. Add the new maintainer as a collaborator with admin access
2. Transfer repository ownership (Settings > Transfer)
3. Transfer npm/package manager ownership
4. Share infrastructure access (CI secrets, domain, etc.)
5. Stay available for questions during the transition (1-2 months)
6. Update the README to reflect new ownership

## What NOT to Do

- **Ghost the project** — Do not just stop responding. Users deserve to know.
- **Delete the repo** — People may depend on it. Archive, do not delete.
- **Hand off to someone you do not trust** — Verify the new maintainer's intentions
- **Feel guilty** — You built something. You shared it. You owe nobody indefinite maintenance.

## The Emotional Part

Letting go of a project is hard. You built it, you cared about it, and it feels like failure. It is not failure. It is responsible stewardship. A clearly archived project with a good README is more helpful than a zombie project with no maintainer.

## Related

- `hub/sustainability.md` — Sustainability overview
- `research/sustainability/preventing-burnout.md` — Burnout prevention
- `research/community/governance-models.md` — Governance and succession
