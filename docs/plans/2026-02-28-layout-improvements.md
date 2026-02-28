# mikebinary.com Layout Improvements Implementation Plan

> **For Claude:** REQUIRED SUB-SKILL: Use superpowers:executing-plans to implement this plan task-by-task.

**Goal:** Transform mikebinary.com from a bare post list into a fully realized personal blog with content discovery, personality, and a polished reading experience.

**Architecture:** Hugo static site. All changes are to layouts (HTML templates in `layouts/`), static CSS (`static/assets/css/styles.css`), content front matter (`content/`), and `hugo.toml`. No new dependencies. A feature branch `redesign/layout-improvements` isolates work from main.

**Tech Stack:** Hugo, Go templates, CSS custom properties, inline SVG icons. Hugo binary is at `./bin/hugo` relative to project root.

---

### Task 1: Create the working branch

**Files:** None (git only)

**Step 1: Create and switch to branch**
```bash
cd /Users/mike10010100/git/michael-blog
git checkout -b redesign/layout-improvements
```
Expected output: `Switched to a new branch 'redesign/layout-improvements'`

**Step 2: Verify**
```bash
git branch --show-current
```
Expected: `redesign/layout-improvements`

---

### Task 2: Accent color system + whitespace fix

**Files:**
- Modify: `static/assets/css/styles.css`

The CSS uses CSS custom properties (variables) defined in `:root` for light mode and overridden in `[data-theme="dark"]`. We add `--accent-rust` to both, and reduce the excessive top padding on `.page-content`.

**Step 1: Add accent color to `:root` block**

In `styles.css`, inside the `:root { ... }` block (currently ends at `--max-width: 680px;`), add on a new line before the closing `}`:
```css
  --accent-rust: #C2693A;
```

**Step 2: Add dark mode override**

Inside the `[data-theme="dark"] { ... }` block (currently ends at `--code-color: #ff7b7b;`), add on a new line before the closing `}`:
```css
  --accent-rust: #E8884F;
```

**Step 3: Reduce page-content top padding**

Find `.page-content` (around line 96–99). Change `padding: 5rem 0;` to `padding: 3rem 0;`.

**Step 4: Verify with local server**
```bash
./bin/hugo serve
```
Open http://localhost:1313 — the gap between the header rule and "RECENT POSTS" should visually tighten. No rust color visible yet.

**Step 5: Commit**
```bash
git add static/assets/css/styles.css
git commit -m "Add rust accent color vars and tighten page-content top padding"
```

---

### Task 3: Social links partial

**Files:**
- Create: `layouts/partials/social-links.html`
- Modify: `static/assets/css/styles.css`

This partial reads `github_username` and `twitter_username` from `hugo.toml` params (already configured). RSS links to `/feed.xml` (Hugo generates this automatically). All icons are inline SVG — no external library.

**Step 1: Create `layouts/partials/social-links.html`**
```html
<div class="social-links">
  {{ with .Site.Params.github_username }}
  <a href="https://github.com/{{ . }}" target="_blank" rel="noopener" aria-label="GitHub">
    <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" aria-hidden="true"><path d="M12 0c-6.626 0-12 5.373-12 12 0 5.302 3.438 9.8 8.207 11.387.599.111.793-.261.793-.577v-2.234c-3.338.726-4.033-1.416-4.033-1.416-.546-1.387-1.333-1.756-1.333-1.756-1.089-.745.083-.729.083-.729 1.205.084 1.839 1.237 1.839 1.237 1.07 1.834 2.807 1.304 3.492.997.107-.775.418-1.305.762-1.604-2.665-.305-5.467-1.334-5.467-5.931 0-1.311.469-2.381 1.236-3.221-.124-.303-.535-1.524.117-3.176 0 0 1.008-.322 3.301 1.23.957-.266 1.983-.399 3.003-.404 1.02.005 2.047.138 3.006.404 2.291-1.552 3.297-1.23 3.297-1.23.653 1.653.242 2.874.118 3.176.77.84 1.235 1.911 1.235 3.221 0 4.609-2.807 5.624-5.479 5.921.43.372.823 1.102.823 2.222v3.293c0 .319.192.694.801.576 4.765-1.589 8.199-6.086 8.199-11.386 0-6.627-5.373-12-12-12z"/></svg>
  </a>
  {{ end }}
  {{ with .Site.Params.twitter_username }}
  <a href="https://twitter.com/{{ . }}" target="_blank" rel="noopener" aria-label="Twitter / X">
    <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" aria-hidden="true"><path d="M18.244 2.25h3.308l-7.227 8.26 8.502 11.24H16.17l-4.714-6.231-5.401 6.231H2.744l7.73-8.835L1.254 2.25H8.08l4.253 5.622zm-1.161 17.52h1.833L7.084 4.126H5.117z"/></svg>
  </a>
  {{ end }}
  <a href="/feed.xml" aria-label="RSS Feed">
    <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" aria-hidden="true"><path d="M6.18 15.64a2.18 2.18 0 0 1 2.18 2.18C8.36 19.01 7.38 20 6.18 20C4.98 20 4 19.01 4 17.82a2.18 2.18 0 0 1 2.18-2.18M4 4.44A15.56 15.56 0 0 1 19.56 20h-2.83A12.73 12.73 0 0 0 4 7.27V4.44m0 5.66a9.9 9.9 0 0 1 9.9 9.9h-2.83A7.07 7.07 0 0 0 4 12.93V10.1z"/></svg>
  </a>
</div>
```

**Step 2: Add social links CSS to `styles.css`** (append at end of file)
```css
/* Social Links */
.social-links {
  display: flex;
  gap: 1.25rem;
  align-items: center;
}

.social-links a {
  color: var(--accent-rust);
  border-bottom: none;
  display: flex;
  align-items: center;
  transition: opacity 0.2s ease;
}

.social-links a:hover {
  opacity: 0.65;
  border-bottom: none;
}

.social-links svg {
  width: 1.2rem;
  height: 1.2rem;
  fill: currentColor;
}
```

**Step 3: Verify build**
```bash
./bin/hugo build 2>&1 | head -20
```
Expected: Build succeeds, no template errors.

**Step 4: Commit**
```bash
git add layouts/partials/social-links.html static/assets/css/styles.css
git commit -m "Add social-links partial with inline GitHub, Twitter, RSS SVG icons"
```

---

### Task 4: Footer redesign

**Files:**
- Modify: `layouts/partials/footer.html`
- Modify: `static/assets/css/styles.css`

The current footer repeats the site title, has an empty `footer-col-2` div, and shows the site description. Replace with: social icons + a single copyright line.

Note: The `<script>` for the dark mode toggle lives in footer.html — keep it.

**Step 1: Replace the entire contents of `layouts/partials/footer.html`**
```html
<footer class="site-footer">
  <div class="wrapper">
    <div class="footer-inner">
      {{ partial "social-links.html" . }}
      <p class="footer-copy">&copy; {{ now.Year }} Michael Paulauski</p>
    </div>
  </div>
</footer>

<script>
  document.addEventListener("DOMContentLoaded", function() {
    var toggle = document.getElementById("theme-toggle");
    if (toggle) {
      toggle.addEventListener("click", function() {
        var currentTheme = document.documentElement.getAttribute("data-theme");
        var newTheme = currentTheme === "dark" ? "light" : "dark";
        document.documentElement.setAttribute("data-theme", newTheme);
        localStorage.setItem("theme", newTheme);
      });
    }
  });
</script>
```

**Step 2: Replace the `/* Footer */` CSS section in `styles.css`**

Find the block starting at `/* Footer */` and ending after `.footer-col-3 p { ... }`. Replace the entire section with:
```css
/* Footer */
.site-footer {
  padding: 3rem 0;
  margin-top: auto;
}

.site-footer .wrapper {
  border-top: 1px solid var(--border-color);
  padding-top: 2rem;
}

.footer-inner {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 1rem;
}

.footer-copy {
  font-size: 0.82rem;
  color: var(--text-muted);
  margin: 0;
}
```

**Step 3: Verify locally**
```bash
./bin/hugo serve
```
Open http://localhost:1313 and scroll to the footer. Should show three rust-colored icons, then `© 2026 Michael Paulauski`. The old "Michael Paulauski's blog" repeated text and the site description should be gone.

**Step 4: Commit**
```bash
git add layouts/partials/footer.html static/assets/css/styles.css
git commit -m "Simplify footer: social icons + copyright, remove redundant copy"
```

---

### Task 5: Homepage intro section

**Files:**
- Modify: `hugo.toml`
- Modify: `layouts/index.html`
- Modify: `static/assets/css/styles.css`

The homepage currently jumps straight from the header to the post list. We add a brief bio + social links intro section, and rename "RECENT POSTS" to "POSTS". Excerpts are enabled here too.

**Step 1: Add `bio` and enable excerpts in `hugo.toml`**

Inside the `[params]` block, add:
```toml
  bio = "Former child actor turned Dev turned DevOps Engineer. Hacking, 3D printing, IoT, and politics."
  show_excerpts = true
```

**Step 2: Replace the entire contents of `layouts/index.html`**
```html
{{ define "main" }}
<div class="home">
  <div class="home-intro">
    <p class="home-bio">{{ .Site.Params.bio }}</p>
    {{ partial "social-links.html" . }}
  </div>

  {{ $posts := where .Site.RegularPages "Section" "posts" }}
  {{ if gt (len $posts) 0 }}
  <h2 class="post-list-heading">Posts</h2>
  <ul class="post-list">
    {{ range $posts }}
    <li>
      <div class="post-card">
        <span class="post-meta">{{ .Date.Format "Jan 2, 2006" }} &middot; {{ .ReadingTime }} min read</span>
        <h3>
          <a class="post-link" href="{{ .RelPermalink }}">{{ .Title }}</a>
        </h3>
        {{ with .Params.tags }}
        <div class="post-tags">
          {{ range . }}
          <a class="tag-pill" href="/tags/{{ . | urlize }}/">{{ . }}</a>
          {{ end }}
        </div>
        {{ end }}
        {{ if .Site.Params.show_excerpts }}
        <div class="post-excerpt">{{ .Summary }}</div>
        {{ end }}
      </div>
    </li>
    {{ end }}
  </ul>
  {{ end }}
</div>
{{ end }}
```

**Step 3: Append new CSS to `styles.css`**
```css
/* Home Intro */
.home-intro {
  margin-bottom: 3rem;
  padding-bottom: 3rem;
  border-bottom: 1px solid var(--border-color);
}

.home-bio {
  font-size: 1.2rem;
  color: var(--text-color);
  margin-bottom: 1.25rem;
  line-height: 1.6;
}

/* Tag Pills */
.post-tags {
  margin-bottom: 0.75rem;
  display: flex;
  flex-wrap: wrap;
  gap: 0.4rem;
}

.tag-pill {
  display: inline-block;
  font-family: var(--font-body);
  font-size: 0.72rem;
  font-weight: 500;
  text-transform: lowercase;
  letter-spacing: 0.04em;
  color: var(--accent-rust);
  border: 1px solid var(--accent-rust);
  border-radius: 2px;
  padding: 0.15em 0.55em;
  transition: all 0.15s ease;
  border-bottom: none;
}

.tag-pill:hover {
  background-color: var(--accent-rust);
  color: #fff;
  border-bottom: none;
}
```

**Step 4: Verify locally**
```bash
./bin/hugo serve
```
Open http://localhost:1313. Should see: bio paragraph, three rust icons, a hairline rule, "Posts" heading, then the post list. Tag pills won't link anywhere yet (tag taxonomy not set up until Task 6). Excerpts should appear under each post title.

**Step 5: Commit**
```bash
git add hugo.toml layouts/index.html static/assets/css/styles.css
git commit -m "Homepage: add bio intro section, enable excerpts, reading time, tag pills"
```

---

### Task 6: Tag taxonomy config + front matter

**Files:**
- Modify: `hugo.toml`
- Modify: `content/posts/on-civility.md`
- Modify: `content/posts/on-far-right-european-rhetoric.md`
- Modify: `content/posts/on-gun-restrictions.md`
- Modify: `content/posts/first-post.md`

Hugo needs taxonomy declared in config, and each post needs a `tags` array in its YAML front matter.

**Step 1: Add taxonomy config to `hugo.toml`** (at the top level, outside `[params]`)
```toml
[taxonomies]
  tag = "tags"
```

**Step 2: Add tags to `content/posts/on-civility.md`**

In the front matter block (`---` ... `---`), add a new line:
```yaml
tags: ["civility", "politics", "reddit"]
```

**Step 3: Add tags to `content/posts/on-far-right-european-rhetoric.md`**
```yaml
tags: ["politics", "europe"]
```

**Step 4: Add tags to `content/posts/on-gun-restrictions.md`**
```yaml
tags: ["politics", "guns"]
```

**Step 5: Add tags to `content/posts/first-post.md`**
```yaml
tags: ["meta"]
```

**Step 6: Verify tag routes exist**
```bash
./bin/hugo serve
```
- Open http://localhost:1313/tags/ — page loads (may be unstyled, that's fine — styled in Task 8)
- Open http://localhost:1313/tags/politics/ — should show the three politics posts
- Click a tag pill on the homepage — should navigate to the correct tag page

**Step 7: Commit**
```bash
git add hugo.toml content/posts/
git commit -m "Add tag taxonomy config and tags to all existing posts"
```

---

### Task 7: Post single template — reading time, tags, prev/next nav

**Files:**
- Modify: `layouts/_default/single.html`
- Modify: `static/assets/css/styles.css`

`.PrevInSection` and `.NextInSection` are Hugo built-ins that return the adjacent post within the same section (ordered by date). `.ReadingTime` is Hugo's auto-calculated word-count-based estimate.

**Step 1: Replace entire contents of `layouts/_default/single.html`**
```html
{{ define "main" }}
<article class="post h-entry" itemscope itemtype="http://schema.org/BlogPosting">
  <header class="post-header">
    <h1 class="post-title p-name" itemprop="name headline">{{ .Title }}</h1>
    <p class="post-meta">
      {{ if not .Date.IsZero }}
      <time class="dt-published" datetime="{{ .Date.Format "2006-01-02T15:04:05Z07:00" }}" itemprop="datePublished">
        {{ .Date.Format "Jan 2, 2006" }}
      </time>
      {{ end }}
      &middot; {{ .ReadingTime }} min read
      {{ if .Params.author }}
      &middot; <span itemprop="author" itemscope itemtype="http://schema.org/Person">
        <span class="p-author h-card" itemprop="name">{{ .Params.author }}</span>
      </span>
      {{ end }}
    </p>
    {{ with .Params.tags }}
    <div class="post-tags">
      {{ range . }}
      <a class="tag-pill" href="/tags/{{ . | urlize }}/">{{ . }}</a>
      {{ end }}
    </div>
    {{ end }}
  </header>

  <div class="post-content e-content" itemprop="articleBody">
    {{ .Content }}
  </div>

  <nav class="post-nav" aria-label="Post navigation">
    <div class="post-nav-prev">
      {{ with .PrevInSection }}
      <span class="post-nav-label">← Previous</span>
      <a href="{{ .RelPermalink }}">{{ .Title }}</a>
      {{ end }}
    </div>
    <div class="post-nav-next">
      {{ with .NextInSection }}
      <span class="post-nav-label">Next →</span>
      <a href="{{ .RelPermalink }}">{{ .Title }}</a>
      {{ end }}
    </div>
  </nav>
</article>
{{ end }}
```

**Step 2: Append post nav CSS to `styles.css`**
```css
/* Post Header Tags */
.post-header .post-tags {
  justify-content: center;
  margin-top: 0.75rem;
  margin-bottom: 0;
}

/* Post Navigation */
.post-nav {
  display: flex;
  justify-content: space-between;
  margin-top: 4rem;
  padding-top: 2rem;
  border-top: 1px solid var(--border-color);
  gap: 2rem;
}

.post-nav-prev {
  flex: 1;
}

.post-nav-next {
  flex: 1;
  text-align: right;
}

.post-nav-label {
  font-size: 0.78rem;
  text-transform: uppercase;
  letter-spacing: 0.08em;
  color: var(--text-muted);
  display: block;
  margin-bottom: 0.35rem;
}

.post-nav a {
  font-family: var(--font-heading);
  font-size: 1.05rem;
  color: var(--heading-color);
  border-bottom: none;
  line-height: 1.3;
}

.post-nav a:hover {
  color: var(--accent-rust);
  border-bottom: none;
}
```

**Step 3: Verify locally**
```bash
./bin/hugo serve
```
Open http://localhost:1313/posts/on-civility/ — should show reading time in the meta line, tag pills below the title, and prev/next nav at the bottom with rust hover on the titles.

**Step 4: Commit**
```bash
git add layouts/_default/single.html static/assets/css/styles.css
git commit -m "Post pages: add reading time, tags, and prev/next navigation"
```

---

### Task 8: Tag taxonomy layout pages

**Files:**
- Create: `layouts/taxonomy/tag.html`
- Create: `layouts/taxonomy/tags.html`
- Modify: `static/assets/css/styles.css`

Hugo uses `layouts/taxonomy/[singular].html` for individual tag listing pages (e.g., `/tags/politics/`) and `layouts/taxonomy/[plural].html` for the all-tags index page (`/tags/`).

**Step 1: Create `layouts/taxonomy/tag.html`** (single tag listing page)
```html
{{ define "main" }}
<div class="taxonomy-page">
  <h1 class="taxonomy-heading">
    Posts tagged <span class="taxonomy-term">{{ .Title }}</span>
  </h1>
  <p class="taxonomy-back"><a href="/tags/">← All tags</a></p>
  <ul class="post-list">
    {{ range .Pages }}
    <li>
      <div class="post-card">
        <span class="post-meta">{{ .Date.Format "Jan 2, 2006" }} &middot; {{ .ReadingTime }} min read</span>
        <h3>
          <a class="post-link" href="{{ .RelPermalink }}">{{ .Title }}</a>
        </h3>
        {{ with .Params.tags }}
        <div class="post-tags">
          {{ range . }}
          <a class="tag-pill" href="/tags/{{ . | urlize }}/">{{ . }}</a>
          {{ end }}
        </div>
        {{ end }}
        {{ if .Site.Params.show_excerpts }}
        <div class="post-excerpt">{{ .Summary }}</div>
        {{ end }}
      </div>
    </li>
    {{ end }}
  </ul>
</div>
{{ end }}
```

**Step 2: Create `layouts/taxonomy/tags.html`** (all-tags index)
```html
{{ define "main" }}
<div class="taxonomy-page">
  <h1 class="taxonomy-heading">All Tags</h1>
  <ul class="tags-index">
    {{ range .Data.Terms.Alphabetical }}
    <li class="tags-index-item">
      <a class="tag-pill tag-pill-lg" href="{{ .Page.RelPermalink }}">{{ .Page.Title }}</a>
      <span class="tag-count">{{ .Count }} {{ if eq .Count 1 }}post{{ else }}posts{{ end }}</span>
    </li>
    {{ end }}
  </ul>
</div>
{{ end }}
```

**Step 3: Append taxonomy CSS to `styles.css`**
```css
/* Taxonomy Pages */
.taxonomy-heading {
  margin-top: 0;
  margin-bottom: 0.5rem;
}

.taxonomy-term {
  color: var(--accent-rust);
}

.taxonomy-back {
  margin-bottom: 3rem;
  font-size: 0.9rem;
}

.taxonomy-back a {
  color: var(--text-muted);
  border-bottom: none;
}

.taxonomy-back a:hover {
  color: var(--heading-color);
  border-bottom: none;
}

.tags-index {
  list-style: none;
  padding: 0;
  margin: 2rem 0 0;
}

.tags-index-item {
  display: flex;
  align-items: center;
  gap: 1rem;
  margin-bottom: 1rem;
}

.tag-pill-lg {
  font-size: 0.85rem;
  padding: 0.25em 0.75em;
}

.tag-count {
  font-size: 0.82rem;
  color: var(--text-muted);
}
```

**Step 4: Verify all tag routes**
```bash
./bin/hugo serve
```
- http://localhost:1313/tags/ — alphabetical tag list with post counts and rust-colored pills
- http://localhost:1313/tags/politics/ — heading says `Posts tagged "politics"` with the term in rust
- http://localhost:1313/tags/meta/ — "First Post" appears
- "← All tags" link on each tag page navigates back to /tags/

**Step 5: Commit**
```bash
git add layouts/taxonomy/ static/assets/css/styles.css
git commit -m "Add tag taxonomy pages: all-tags index and per-tag post listing"
```

---

### Task 9: Expand the About page

**Files:**
- Modify: `content/about.md`
- Create: `layouts/_default/page.html`
- Modify: `layouts/_default/list.html`

The about page has `layout: page` in its front matter. Hugo resolves this by looking for `layouts/_default/page.html` before falling back to `single.html`. We create that layout to inject the social links below the content.

We also update `list.html` so the `/posts/` archive page matches the homepage post card format.

**Step 1: Replace contents of `content/about.md`**
```markdown
---
layout: page
title: About
permalink: /about/
---

Former child actor turned Dev turned DevOps Engineer. I spend my time on hacking, 3D printing, IoT, and politics — not necessarily in that order.

I work in infrastructure and platform engineering. This blog is where I think out loud about technology, current events, and whatever else is on my mind.

All thoughts here are my own and not my employers'.
```

**Step 2: Create `layouts/_default/page.html`**
```html
{{ define "main" }}
<article class="post h-entry">
  <header class="post-header">
    <h1 class="post-title">{{ .Title }}</h1>
  </header>
  <div class="post-content e-content">
    {{ .Content }}
  </div>
  {{ partial "social-links.html" . }}
</article>
{{ end }}
```

**Step 3: Replace contents of `layouts/_default/list.html`**

Update to match the post card format from `index.html` (adds reading time + tags):
```html
{{ define "main" }}
<div class="home">
  <h1 class="page-heading">{{ .Title }}</h1>
  {{ .Content }}

  {{ if gt (len .Pages) 0 }}
  <ul class="post-list">
    {{ range .Pages }}
    <li>
      <div class="post-card">
        <span class="post-meta">{{ .Date.Format "Jan 2, 2006" }} &middot; {{ .ReadingTime }} min read</span>
        <h3>
          <a class="post-link" href="{{ .RelPermalink }}">{{ .Title }}</a>
        </h3>
        {{ with .Params.tags }}
        <div class="post-tags">
          {{ range . }}
          <a class="tag-pill" href="/tags/{{ . | urlize }}/">{{ . }}</a>
          {{ end }}
        </div>
        {{ end }}
        {{ if .Site.Params.show_excerpts }}
        <div class="post-excerpt">{{ .Summary }}</div>
        {{ end }}
      </div>
    </li>
    {{ end }}
  </ul>
  {{ end }}
</div>
{{ end }}
```

**Step 4: Verify locally**
```bash
./bin/hugo serve
```
- http://localhost:1313/about/ — expanded bio, social icons in rust below the text
- http://localhost:1313/posts/ — posts archive with matching card format

**Step 5: Commit**
```bash
git add content/about.md layouts/_default/page.html layouts/_default/list.html
git commit -m "Expand about page with social links; update list template to match post cards"
```

---

### Task 10: Final verification and comparison

**Step 1: Full clean build**
```bash
./bin/hugo --cleanDestinationDir 2>&1
```
Expected: Build completes with no errors or warnings.

**Step 2: Serve and do a full walkthrough**
```bash
./bin/hugo serve
```
Check every route:
- http://localhost:1313/ — bio + social icons, "Posts" heading, post list with excerpts + reading time + rust tag pills
- http://localhost:1313/posts/on-civility/ — reading time in meta, tags below title, prev/next nav at bottom with rust hover
- http://localhost:1313/posts/on-far-right-european-rhetoric/ — same as above
- http://localhost:1313/about/ — expanded bio + social icons
- http://localhost:1313/tags/ — alphabetical tag list
- http://localhost:1313/tags/politics/ — three posts, "politics" heading in rust
- http://localhost:1313/feed.xml — RSS feed loads
- Toggle dark mode — accent rust color adjusts to `#E8884F`, all elements look correct
- Resize browser to 390px width — mobile layout still looks clean

**Step 3: Compare against production**

Open https://mikebinary.com alongside http://localhost:1313 to confirm the improvements.

**Step 4: Push branch for review**
```bash
git push -u origin redesign/layout-improvements
```
The branch is now available on GitHub for PR when ready.
