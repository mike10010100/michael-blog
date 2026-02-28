# mikebinary.com Layout Redesign

**Date:** 2026-02-28
**Status:** Approved, ready for implementation

## Goal

Improve mikebinary.com from a bare-bones blog list into a fully realized personal site with
content discovery, personality, and a polished reading experience — while keeping the
minimalist/modern aesthetic intact.

## Color & Visual System

Add a single warm terracotta accent color used sparingly for interactive and label elements only.

- Light mode accent: `#C2693A`
- Dark mode accent: `#E8884F`
- Uses: tag pills, social link icons, subtle hover states on post titles
- Everything else stays in the existing black/white/gray palette
- No decorative borders, backgrounds, or fills with the accent

## Homepage

### Intro Section
Between the header rule and the post list, add a short intro section:
- Two-line bio drawn from about.md ("Former child actor turned Dev turned DevOps Engineer...")
- Three social icons: GitHub, Twitter, RSS — in accent color
- Followed by a second hairline rule to separate from the post list

### Post List
- Rename "RECENT POSTS" → "POSTS"
- Enable excerpts (`show_excerpts = true` in hugo.toml)
- Each post card shows: date · reading time · title · tag pills · excerpt
- Tag pills use accent color, link to the tag taxonomy page

## Post Pages

### Header
- Title (centered, existing style)
- Date · reading time · tag pills (centered, below title)
- Tags link to their taxonomy pages

### Footer (within post)
- Hairline rule
- Prev/next post navigation: `← Previous Title` on the left, `Next Title →` on the right
- Full-width, spanning the content column

### No separate "back" link needed — the site title in the header already serves this.

## Tag Taxonomy

### /tags/ index page
- Lists all tags alphabetically with post count
- Simple, clean — no tag cloud gimmicks

### /tags/[tag]/ listing pages
- Same layout as homepage post list (date · reading time · title · excerpt)
- Heading: `Posts tagged "[tag]"` with accent-colored tag name
- Link back to all tags

### Front matter
- Retroactively add `tags` to the 4 existing posts:
  - on-civility: [civility, politics, reddit]
  - on-far-right-european-rhetoric: [politics, europe]
  - on-gun-restrictions: [politics, guns]
  - first-post: [meta]

## About Page

### Content
- Expand the two-sentence bio into a short proper paragraph
- Add a "What I write about" sentence (tech, politics, personal)
- Add social links section (same icons as homepage intro — GitHub, Twitter, RSS)

## Footer

### Simplification
- Remove the redundant "Michael Paulauski's blog" title (already in the header)
- Remove the empty `footer-col-2` div
- Replace the three-column layout with: centered social icons (GitHub, Twitter, RSS) + a single line of muted copyright/year text
- Remove the site description (it's in the meta tag, it doesn't need to be visible)

## Whitespace

- Reduce `.page-content` top padding from `5rem` to `3rem` — the gap between the header
  rule and content is currently ~130px, which is too much
- Header padding stays at `3rem` (it's correct)

## Technical Notes

- Hugo taxonomy: add `[taxonomies] tag = "tags"` to hugo.toml
- Reading time: use Hugo's built-in `.ReadingTime` (auto-calculated, no config needed)
- Prev/next: use `.PrevInSection` / `.NextInSection` in the single.html template
- Social icons: inline SVGs (GitHub, Twitter/X, RSS) — no external icon library
- New partials needed: `tags.html` (tag pills), `social-links.html` (icon row)
- New layouts needed: `layouts/taxonomy/tag.html`, `layouts/taxonomy/tags.html`
- Excerpt length: Hugo default (~70 words) — no custom truncation needed

## Implementation Order

1. Branch from main: `redesign/layout-improvements`
2. Color system update (CSS vars)
3. Whitespace fix
4. Social links partial + footer redesign
5. Homepage intro section
6. Enable excerpts + update post card template with reading time + tags
7. Post front matter tags
8. Post single template: reading time + tags + prev/next nav
9. Tag taxonomy pages
10. About page expansion
