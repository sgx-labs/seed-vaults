---
title: "Design for Developers — Ship Good-Looking Products Solo"
tags: [building, design, ui, ux, developer]
content_type: research
domain: business
---

# Design for Developers — Ship Good-Looking Products Solo

## The Developer Design Trap

Most developers think design means "making things pretty." It does not. Design means making things clear. If a user can understand what your product does and how to use it in 10 seconds, you have good design. You do not need to be a designer.

## The 80/20 of Design

These five things cover 80% of good design:

### 1. Consistent spacing
- Pick a spacing scale: 4, 8, 16, 24, 32, 48, 64 pixels
- Use the same spacing everywhere
- More whitespace = more professional looking

### 2. Fewer colors
- One primary color (for buttons and key actions)
- Gray for text and backgrounds (2-3 shades)
- One accent for warnings or highlights
- That is it. Three colors total.

### 3. Good typography
- One font family (Inter, System UI, or the default sans-serif)
- 3 sizes max: heading, body, small
- Line height of 1.5 for body text
- Max line width of 65-75 characters

### 4. Alignment
- Left-align everything (unless centering a hero section)
- Use a grid (even a simple 12-column grid helps)
- Make sure elements line up vertically

### 5. Contrast
- Dark text on light background (or vice versa)
- Buttons stand out from the background
- Important actions are visually prominent

## Tools That Make You Look Like a Designer

| Tool | What it does | Cost |
|------|-------------|------|
| shadcn/ui | Pre-built React components | Free |
| Tailwind UI | Component templates | $299 one-time |
| Heroicons | Icon set that works with Tailwind | Free |
| Lucide Icons | Another great icon set | Free |
| Figma | Design mockups before coding | Free tier |
| Coolors | Color palette generator | Free |
| Unsplash | Stock photos | Free |
| Screely | Browser mockups for screenshots | Free |

## Landing Page Design Patterns

1. **Hero:** Full-width, centered text, one CTA button, optional screenshot
2. **Features:** 3-column grid, icon + title + one-line description each
3. **Social proof:** Testimonial cards or logo bar
4. **Pricing:** 2-3 tier cards side by side (see `templates/pricing-page.md`)
5. **Footer:** Links, legal, social icons

## Component Libraries Worth Using

- **shadcn/ui** — Copy-paste React components. Not a dependency, just code you own.
- **DaisyUI** — Tailwind component library with themes
- **Radix UI** — Headless components for accessibility
- **Chakra UI** — Opinionated but fast for prototyping

## Design Review Checklist

Before launching, check:
- [ ] Does the hero communicate what the product does in 5 seconds?
- [ ] Is there one clear CTA above the fold?
- [ ] Does it look good on mobile? (check this for real)
- [ ] Are buttons large enough to tap on mobile?
- [ ] Is text readable (14px minimum for body, good contrast)?
- [ ] Do loading states exist (spinners, skeletons)?
- [ ] Are error states handled (form validation, 404 page)?

## The Best Design Advice for Developers

Copy what works. Find 3-5 products in your space that look good. Screenshot them. Use similar layout, spacing, and patterns. This is not plagiarism — it is design pattern reuse. Every professional designer does this.
