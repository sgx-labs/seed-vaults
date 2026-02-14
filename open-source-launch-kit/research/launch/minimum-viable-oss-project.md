---
title: "What Is the Minimum Viable Open Source Project?"
tags: [launch, mvp, project-structure, checklist]
content_type: research
domain: engineering
---

# What Is the Minimum Viable Open Source Project?

## The Question

What do you actually need before making a project public? Not the ideal state — the minimum that will not embarrass you.

## The Minimum (6 Files)

### 1. README.md
The only file every visitor reads. Must answer three questions in 30 seconds:
- What is this?
- Why should I care?
- How do I install it?

See `templates/readme-template.md` for a fill-in template.

### 2. LICENSE
Without a license, your code is "all rights reserved" by default. Nobody can legally use it. Pick one and add it before going public. See `research/launch/choosing-a-license.md`.

### 3. .gitignore
Language-appropriate gitignore. Use GitHub's templates: github.com/github/gitignore

### 4. Working code that does one thing well
Ship the smallest useful version. One feature, working reliably, is better than five features half-built. Users can tolerate missing features. They cannot tolerate broken features.

### 5. Install instructions that work
Test on a clean machine (or a fresh container). If your install instructions fail for the first person who tries them, you have lost that user forever.

### 6. At least one test
Proves the project is not abandoned. Shows contributors how to write tests. Gives CI something to run.

## The Ideal (Add These Before Public Launch)

- CONTRIBUTING.md — how to contribute
- CHANGELOG.md — track changes from the start
- CODE_OF_CONDUCT.md — Contributor Covenant
- SECURITY.md — vulnerability reporting process
- CI workflow — tests run on every PR
- Issue templates — bug report, feature request
- GitHub Description, Topics, Social preview image

## What You Do NOT Need

- A website or documentation site (README is enough at first)
- A logo (text name is fine)
- Complete API documentation (add as you go)
- A Discord server (wait until you have active users)
- Perfection (ship it, then iterate)

## The Golden Rule

**If a stranger clones your repo, can they get it working in under 5 minutes without messaging you?** If yes, you are ready to launch. If no, fix the setup experience first.

## Related

- `hub/launch.md` — Full pre-launch checklist
- `research/docs/readme-that-converts.md` — Writing READMEs that convert
- `research/docs/writing-install-instructions.md` — Install docs that work
