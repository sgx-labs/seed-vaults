---
title: "How Do I Get My Project on Awesome Lists?"
tags: [awesome-lists, directories, github, discovery, marketing]
content_type: research
domain: engineering
---

# How Do I Get My Project on Awesome Lists?

## The Question

Awesome Lists are curated GitHub repositories that list the best tools in a category. Getting listed drives persistent discovery. How do you get accepted?

## What Are Awesome Lists?

Curated collections of links organized by topic. The parent list — sindresorhus/awesome — has 300K+ stars. Category-specific lists (awesome-go, awesome-python, awesome-react) have 10K-150K+ stars each.

Why they matter:
- Appear in Google search results for "[category] tools"
- Developers bookmark and reference them
- Being listed is a credibility signal
- Traffic is ongoing, not a one-time spike

## Finding the Right Lists

1. Search GitHub for `awesome-[your-category]`
2. Check the parent list: github.com/sindresorhus/awesome
3. Look at where similar tools are listed
4. Search: `site:github.com awesome [your-tool-category]`

## Submission Requirements

Most awesome lists have strict quality requirements. Common rules:

- **Project must be notable** — Real users, not just the author
- **Active maintenance** — Recent commits, responsive to issues
- **Quality documentation** — Clear README with install instructions
- **Proper license** — OSI-approved open source license
- **No duplicates** — Must add something different from existing entries
- **Format compliance** — Follow the list's specific formatting rules

### awesome-go (Example of Strict Requirements)

- Must have a CI pipeline (GitHub Actions, etc.)
- Must have tests with reasonable coverage
- Must handle errors properly (no `_` error ignoring)
- README must include install instructions and examples
- Must be actively maintained

## Submission Process

1. **Read the contributing guide** — Every list has one. Read it completely.
2. **Check existing entries** — Make sure your tool is not already listed or too similar.
3. **Format your entry correctly:**
   ```markdown
   - [Your Project](https://github.com/yourorg/your-project) - One-line description without period.
   ```
4. **Open a PR** — Follow the template. Explain why your project belongs.
5. **Be patient** — Reviews can take days to weeks.

## Tips for Getting Accepted

- **Do not submit on day one** — Have some usage history, stars, and real users first
- **One list at a time** — Submitting to 10 lists simultaneously looks spammy
- **Contribute first** — Fix typos or add other worthy projects before submitting yours
- **Follow up politely** — If no response after 2 weeks, a gentle ping is okay
- **Accept rejection gracefully** — If rejected, ask why and improve

## Notable Awesome Lists by Category

| Category | List | Stars |
|----------|------|-------|
| General | sindresorhus/awesome | 300K+ |
| Go | avelino/awesome-go | 130K+ |
| Python | vinta/awesome-python | 220K+ |
| JavaScript | sorrycc/awesome-javascript | 30K+ |
| Rust | rust-unofficial/awesome-rust | 45K+ |
| CLI | agarrharr/awesome-cli-apps | 15K+ |
| Self-hosted | awesome-selfhosted/awesome-selfhosted | 200K+ |
| MCP Servers | punkpeye/awesome-mcp-servers | 30K+ |

## Related

- `research/marketing/directory-listing-strategy.md` — Full directory strategy
- `hub/marketing.md` — Marketing overview
- `research/launch/first-100-stars.md` — Building traction
