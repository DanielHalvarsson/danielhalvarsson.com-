# danielhalvarsson.com

Personal site of Daniel Halvarsson, including the `/write` Typst editor.

## Stack

Static HTML served directly by Nginx. No build step, no framework. Each page is a self-contained HTML file with inline CSS and no external dependencies beyond Google Fonts.

The exception is `/write`, which bundles CodeMirror locally so the editor does not depend on cross-origin runtime module imports.

---

## Design System

Every page must feel like it belongs to the same site. Do not deviate from the choices below.

### Colors

```css
--bg:     #0d1117;               /* Deep slate — page background */
--bg2:    #151c26;               /* Slightly lighter — alternating sections */
--bg3:    #1c2535;               /* Cards, publication listings */
--border: rgba(255,255,255,0.07);/* Dividers, card borders */
--text:   #e8e0d4;               /* Warm off-white — body copy */
--muted:  #8a8a8a;               /* Secondary text, inactive nav links */
--amber:  #e8a040;               /* Primary accent — use sparingly */
--amber2: #c4782a;               /* Darker amber — hover states */
--green:  #6baa7c;               /* Published / success */
--red:    #c46a6a;               /* Error / urgent */
--white:  #f5f0ea;               /* Headings, logo, emphasis */
```

Active nav link and key highlights use `rgb(201, 151, 46)` — a slightly cooler gold that reads well against both dark backgrounds and `--muted` neighbors.

### Typography

| Role | Family | Weights | Notes |
|---|---|---|---|
| Display / headings | Cormorant Garamond | 300, 400, 600 | Italic for hero headings and logo |
| Body copy | Outfit | 300, 400, 500 | Most prose |
| Monospace labels | DM Mono | 300, 400 | Section numbers, tags, metadata, nav links |

Google Fonts import (put in every `<head>`):

```html
<link rel="preconnect" href="https://fonts.googleapis.com" />
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin />
<link href="https://fonts.googleapis.com/css2?family=Cormorant+Garamond:ital,wght@0,300;0,400;0,600;1,300;1,400&family=DM+Mono:wght@300;400&family=Outfit:wght@300;400;500&display=swap" rel="stylesheet" />
```

### Navigation

Every page except `/write` carries the same fixed nav. Copy this CSS and HTML verbatim.

**CSS:**

```css
nav {
  position: fixed; top: 0; left: 0; right: 0; z-index: 100;
  height: 60px;
  padding: 0 48px;
  display: flex; justify-content: space-between; align-items: center;
  background: rgba(13, 17, 23, 0.85);
  backdrop-filter: blur(12px);
  -webkit-backdrop-filter: blur(12px);
  border-bottom: 1px solid rgba(255, 255, 255, 0.07);
}

.nav-logo {
  font-family: 'Cormorant Garamond', serif;
  font-size: 1.1rem;
  font-weight: 300;           /* must be 300 — do not let this drift to 400/500 */
  font-style: italic;         /* must be italic — do not override */
  color: #f5f0ea;
  text-decoration: none;
  letter-spacing: 0.02em;
}

.nav-links { display: flex; gap: 2rem; }

.nav-links a {
  font-family: 'DM Mono', monospace;
  font-size: 0.78rem;
  font-weight: 300;
  letter-spacing: 0.46px;     /* not em-based — fixed px */
  color: #8a8a8a;
  text-decoration: none;
  transition: color 0.2s;
  /* NO text-transform: uppercase */
}

.nav-links a:hover  { color: #e8a040; }
.nav-links a.active { color: rgb(201, 151, 46); }

@media (max-width: 640px) {
  nav { padding: 0 24px; }
  .nav-links { gap: 1.2rem; }
}
```

**HTML** (add `class="active"` to the link matching the current page):

```html
<nav>
  <a href="/" class="nav-logo">Daniel Halvarsson</a>
  <div class="nav-links">
    <a href="/research">Research</a>
    <a href="/presentations">Presentations</a>
    <a href="/system">System</a>
    <a href="/builds">Builds</a>
    <a href="/write">Write</a>
  </div>
</nav>
```

Link order is fixed: Research → Presentations → System → Builds → Write.

### Layout

- Content sections: `padding: 5rem 2.5rem` (desktop), `3.5rem 1.5rem` (mobile)
- Content container: `max-width: 1000px; margin: 0 auto`
- Text columns: `max-width: 720px` for body prose, up to `1000px` for grids
- Alternating section backgrounds: `--bg` / `--bg2`

### Section labels

```css
.section-label {
  font-family: 'DM Mono', monospace;
  font-size: 0.7rem;
  letter-spacing: 0.2em;
  text-transform: uppercase;
  color: #e8a040;             /* amber */
  display: flex; align-items: center; gap: 0.8rem;
}
.section-label::after {
  content: '';
  flex: 1; max-width: 80px; height: 1px;
  background: #e8a040;
  opacity: 0.4;
}
```

Rendered as: `01 — Section Title`

### Cards

Publication and build cards use `--bg3` background with a `1px solid rgba(255,255,255,0.07)` border. On hover: `border-color: rgba(255,255,255,0.15)` and `transform: translateY(-2px)`.

Status tags on publication cards:

```css
.pub-status.working   { color: #e8a040; }   /* amber */
.pub-status.published { color: #6baa7c; }   /* green */
.pub-status.report    { color: #8a8a8a; }   /* muted */
```

### Anti-patterns

- No gradients, glowing effects, or decorative particles
- No SaaS landing page clichés (hero CTAs, pricing tables, testimonials)
- No bullet-point lists in body text — write in prose
- No external CSS or JS files (everything inline per file)
- No `text-transform: uppercase` on nav links
- No absolute URLs in internal links — use `/path` not `https://danielhalvarsson.com/path`

---

## Site Structure

```
/index.html                        Landing page
/research/index.html               Publication listing hub
/research/energy/index.html        Energy paper (dedicated page)
/research/energy/manuscript.pdf    Paper PDF
/system/index.html                 The Waking System (architecture essay)
/builds/index.html                 Build log index
/builds/printwise/index.html       Printwise build post
/builds/fragment-library/index.html Fragment Library build post
/presentations/index.html          Presentations listing
/write/index.html                  Typst editor (standalone — different UI)
/write/auth.html                   Write editor login
```

## Adding Content

**New publication** — open `/research/index.html`, find `<!-- ADD MORE PUBLICATIONS HERE -->`, copy an existing card block.

**New build post** — create `/builds/[slug]/index.html` from an existing post, add entry to top of `/builds/index.html`.

**New research page** — create `/research/[slug]/index.html` from `/research/energy/index.html` as template, link the card in `/research/index.html`.

## Write Editor

- `write/codemirror-loader.src.js` — CodeMirror entry module
- `write/codemirror-loader.bundle.js` — generated browser bundle (committed)

Rebuild the bundle after changing CodeMirror imports:

```bash
npm install
npm run build:write-codemirror
```

## Deployment

```bash
git add . && git commit -m "description" && git push
```

Nginx serves directly from this directory. Files are live the moment they're saved. No build step, no deploy step.

## Server

- Machine: Lenovo X1 Carbon, Ubuntu, always-on
- Nginx root: `/home/server_lama/server-projects/danielhalvarsson.com`
- SSL: Let's Encrypt, auto-renewing
- Domain: `danielhalvarsson.com` + `www.danielhalvarsson.com`
