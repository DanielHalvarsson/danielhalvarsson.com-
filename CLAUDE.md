# danielhalvarsson.com

Personal academic site for Daniel Halvarsson, senior researcher at the Ratio Institute, Stockholm.

## What This Is

A static site served directly by Nginx from this repo. No build step, no framework, no dependencies beyond Google Fonts. Each page is a self-contained HTML file with inline CSS.

Nginx serves from this directory: edits to HTML files are live immediately.

## Site Structure

```
/index.html                          → Landing page
/research/index.html                 → Publication listing hub
/research/energy/index.html          → Energy paper (dedicated page)
/research/energy/manuscript.pdf      → Paper PDF
/system/index.html                   → The Waking System (architecture essay)
/builds/index.html                   → Build log index
/builds/waking-system/index.html     → First build post
```

## Design System

**Do not deviate from the established design language.** Every page must feel like it belongs to the same site.

### Colors
```css
--bg:       #0d1117;                 /* Deep slate background */
--bg2:      #151c26;                 /* Slightly lighter sections */
--bg3:      #1c2535;                 /* Card backgrounds */
--border:   rgba(255,255,255,0.07);
--text:     #e8e0d4;                 /* Warm off-white body text */
--muted:    #8a8a8a;                 /* Secondary text */
--amber:    #e8a040;                 /* Primary accent — use sparingly */
--amber2:   #c4782a;                 /* Darker amber */
--green:    #6baa7c;                 /* Published/success */
--red:      #c46a6a;                 /* Urgent/overdue */
--white:    #f5f0ea;                 /* Headings, emphasis */
```

### Typography
```
Display headings:  Cormorant Garamond (300, 400, 600, italic)
Body text:         Outfit (300, 400, 500)
Labels/technical:  DM Mono (300, 400) — section numbers, tags, metadata
```

### Google Fonts
```html
<link href="https://fonts.googleapis.com/css2?family=Cormorant+Garamond:ital,wght@0,300;0,400;0,600;1,300;1,400&family=DM+Mono:wght@300;400&family=Outfit:wght@300;400;500&display=swap" rel="stylesheet" />
```

### Design Principles
- Restrained. Whitespace and typography do the work. No gradients, shadows, or decorative elements.
- Numbered sections: `01 — Title`, `02 — Title`, etc.
- Cards with `--bg3` background for featured items, data summaries, publication listings.
- Monospace (DM Mono) for section numbers, tags, technical labels. Uppercase, letter-spaced.
- Serif (Cormorant Garamond italic) for major headings.
- Amber accent used sparingly — key findings, active links, tags.
- Fixed frosted-glass nav at top: `[Daniel Halvarsson]  [Research] [System] [Builds]`
- Full-width sections with max-width content containers (~720-800px for text).
- Every page must be responsive and look excellent on mobile.

### Nav
Present on every page:
```html
<nav>
  <a href="/" class="nav-logo">Daniel Halvarsson</a>
  <div class="nav-links">
    <a href="/research">Research</a>
    <a href="/system">System</a>
    <a href="/builds">Builds</a>
  </div>
</nav>
```

### Tool pages

`/write` and `/presentations/edit/` are full-screen tool UIs, not content pages. They share
the same typefaces as the rest of the site but use an adapted color palette — slightly more
muted, with stronger borders — appropriate for focused work environments. Do not apply the
content-page color tokens to these pages.

Specifically, `/write` uses a Sandy amber (`#d4a574`) rather than the content-page amber
(`#e8a040`). This is intentional — the calmer tone reduces distraction while writing.

The required Google Fonts import for **all** pages (content and tool) is:
```html
<link href="https://fonts.googleapis.com/css2?family=Cormorant+Garamond:ital,wght@0,300;0,400;0,600;1,300;1,400&family=DM+Mono:wght@300;400&family=Outfit:wght@300;400;500&display=swap" rel="stylesheet">
```
Never load fewer weights or omit italic variants — the italic is used for brand marks and
display headings across all pages.

### Anti-Patterns
- No generic AI aesthetic (gradients, glowing orbs, particles)
- No SaaS landing page clichés
- No bullet-point lists in body text — write in prose
- No external CSS or JS files — everything inline in each HTML file
- No build tools, bundlers, or preprocessors

## Presentations (`/presentations/*.html`)

Presentations are standalone `.html` files in `/presentations/`. They have their own
design language (light background, Playfair Display / Source Sans 3 typography) and are
independent of the main site design system above.

### The HTML-is-the-data rule

**All slide content must be written as static HTML. No JavaScript may generate or
overwrite content elements.**

This is the single most important rule for presentations. It exists because:
- The WYSIWYG editor at `/presentations/edit/` edits the live DOM and saves it back —
  if JS re-renders content on load, all edits are silently overwritten.
- LLMs can read and modify static HTML directly without needing to understand a renderer.
- Static HTML is durable, archivable, and works without JavaScript.

### Required structure

```html
<main id="main">
  <section id="[slug]" class="section">
    <h2>Section title</h2>
    <p>All content as plain HTML — no templates, no data arrays.</p>
    <!-- tables, notes, chips, variable rows — all static markup -->
  </section>

  <section id="[slug-2]" class="section">
    ...
  </section>
</main>
```

- `<main id="main">` is the content root the editor targets.
- Each slide/topic is a `<section id="[slug]">` element — the unit of composition.
- All content is inline in the markup. Nothing is generated from a data array.

### What JavaScript is allowed

```javascript
// YES — reads existing <section> elements from the DOM, builds spine nav
renderSpine();

// YES — scroll tracking, keyboard shortcuts, section highlighting
window.addEventListener('scroll', updateActive);
document.addEventListener('keydown', ...);

// NO — generates content from data, overwrites #main
document.getElementById('main').innerHTML = sections.map(renderSection).join('');

// NO — any pattern that writes slide content on load
renderAll();
```

`renderSpine()` is explicitly permitted: it reads section IDs and titles *from* the
static HTML and builds navigation. Data flows from HTML → nav, not the other way around.

### Existing presentations

`arsredovisningar.html` and `scb-register-ratio-v3.html` were originally generated with
a JS renderer. That renderer has been neutralised (the `getElementById('main').innerHTML`
call redirected to a throwaway object) and their content is now static. Treat them as
static files. Do not restore the renderer. If substantial restructuring is needed,
regenerate them as fully static HTML from scratch.

### Adding a new presentation

1. Create `/presentations/[slug].html` as a self-contained HTML file.
2. Follow the static HTML rule above — write all content directly into `<section>` elements.
3. Add an entry to `/presentations/index.html`.
4. The file is immediately available in the editor via the `server ▾` panel.

## Adding Content

### New publication
Open `/research/index.html`. Find the `<!-- ADD MORE PUBLICATIONS HERE -->` comment. Copy an existing card block, edit the fields. The card template is self-documenting.

### New build post
1. Create `/builds/[slug]/index.html` using an existing post as template
2. Add an entry to `/builds/index.html` at the top of the list (reverse chronological)
3. Match the design language exactly

### New research paper page
1. Create `/research/[slug]/index.html` using `/research/energy/index.html` as template
2. Update the corresponding card in `/research/index.html` to link to the new page

## Deployment

Files are live the moment they're saved — Nginx serves directly from this repo.

```bash
# After making changes:
git add . && git commit -m "Description of change" && git push
```

No build step. No deploy step. Commit is the record.

## Server Details

- Server: Lenovo X1 Carbon, Ubuntu, always-on
- Nginx root: this directory (`/home/server_lama/server-projects/danielhalvarsson.com`)
- SSL: Let's Encrypt, auto-renewing
- Domain: danielhalvarsson.com + www.danielhalvarsson.com

## Owner

Daniel Halvarsson
Senior Researcher, The Ratio Institute, Stockholm
daniel.halvarsson@ratio.se