# Website Design & Implementation Plan
## hermanfreiman.com

*Herman (Zvi) Freiman — Holocaust Survival Research*

---

## 1. Requirements Summary

| # | Requirement | Notes |
|---|-------------|-------|
| 1 | Clean, readable layout | Not cluttered; dignified, memorial-appropriate design |
| 2 | Fully responsive | Mobile phones through large desktop monitors |
| 3 | No database | Static content only, no server-side runtime needed |
| 4 | Single truth point | Markdown file(s) as the sole content source |
| 5 | Easy to maintain | Admin (non-developer) can update content by editing Markdown |
| 6 | Deploy on Hetzner VM | Ubuntu 26.04 LTS; Node.js v22, Python 3.14 already present |
| 7 | Low traffic, temporary | A handful of family members; site may run for 1–2 months |

---

## 2. Proposed Site Structure

The current monolithic `Herman_Freiman_Research_Report.md` should be split into dedicated per-page Markdown files. This maps directly to website pages and keeps each content file focused and easy to edit.

```
content/
├── index.md              # Home — brief biography + hero photo
├── timeline.md           # Sections 4.1–4.4 (Chronological Timeline)
├── family.md             # Section 5 (Family Tree) + Section 1 (Biography)
├── documents.md          # Sections 7–8 (Restitution + Transcriptions)
├── history.md            # Section 9 (Historical Context: Jewish Boryslaw)
├── gallery.md            # Photo gallery (Personal/ images)
└── research.md           # Section 10 (Resources & Next Steps) + discrepancies
```

Editing a page = opening one Markdown file and saving. No code changes needed.

---

## 3. Technology Options

### Option A — Hugo

**What it is:** A static site generator written in Go. Converts Markdown → HTML at build time. Distributed as a single binary.

| | |
|-|-|
| **Language** | Go (single self-contained binary, no runtime to manage) |
| **Build time** | < 1 second for a site this size |
| **Markdown support** | Native; front-matter (YAML/TOML) per file |
| **Themes** | Large library; several elegant, minimal memorial/content themes |
| **Serving** | Build output is plain HTML/CSS/JS → serve with nginx |
| **Maintenance** | Edit `.md` file → run `hugo` → static files update |

**Pros**
- No Node.js/Python dependency at build time — single binary install (`apt install hugo` or download)
- Fastest build of any option
- Very mature and widely used for personal/family sites
- Rich theme ecosystem; several appropriate minimal themes (PaperMod, Ananke, Hugo Coder)
- Built-in image processing (resize, WebP conversion) for the photo gallery
- Excellent multilingual support (useful if Hebrew sections grow)

**Cons**
- Go templating syntax (`{{ .Title }}`) is unfamiliar at first
- Themes require understanding Hugo's directory layout (layouts, archetypes, static)
- Less flexible if you ever want interactive components

**Effort estimate:** ~2–3 hours to scaffold, configure theme, and wire up content

---

### Option B — Eleventy (11ty)

**What it is:** A Node.js static site generator. Highly flexible, minimal opinions. Reads Markdown files directly.

| | |
|-|-|
| **Language** | Node.js (v22 already installed) |
| **Build time** | 2–5 seconds for a site this size |
| **Markdown support** | Native via markdown-it; front-matter per file |
| **Themes** | Smaller library; more DIY on CSS, or use a starter kit |
| **Serving** | Build output is plain HTML/CSS/JS → serve with nginx |
| **Maintenance** | Edit `.md` file → run `npx @11ty/eleventy` → static files update |

**Pros**
- Node.js already present; `npm install` is the only setup step
- Extremely flexible — any template language (Nunjucks, Liquid, HTML)
- No magic; you fully control the HTML structure
- Can use any CSS framework (Tailwind, Pico, plain CSS)
- Good for long-term maintainability if comfort with HTML/CSS exists

**Cons**
- No theme ecosystem comparable to Hugo — CSS/layout work is mostly manual
- More initial boilerplate to write than Hugo+theme
- Requires more decisions upfront (which template engine, which CSS approach)

**Effort estimate:** ~4–6 hours (more layout work required)

---

### Option C — Astro

**What it is:** A modern Node.js framework built specifically for content-heavy sites. Markdown-first, ships zero JavaScript to the browser by default.

| | |
|-|-|
| **Language** | Node.js (v22 already installed) |
| **Build time** | 5–15 seconds |
| **Markdown support** | First-class; MDX supported for richer content |
| **Themes** | Growing library; several clean, modern themes |
| **Serving** | Static build output → serve with nginx |
| **Maintenance** | Edit `.md` file → run `npm run build` → static files update |

**Pros**
- Modern developer experience, excellent documentation
- Content collections: structured Markdown with schema validation
- Zero JS shipped to browser by default — fastest page loads
- Component-based layout with `.astro` files (similar to HTML)
- Starlight (docs theme) or plain Astro themes work well for this type of site

**Cons**
- Newest of the three options; slightly more tooling complexity
- `npm run build` step can take 10–15s (unimportant here but notable)
- More abstraction than Eleventy; less mature than Hugo
- Content collections require a small schema definition file

**Effort estimate:** ~3–4 hours

---

### Option D — Plain HTML + Pandoc + Nginx

**What it is:** Use Pandoc (a universal document converter) to render Markdown → HTML inside a handwritten HTML template. A shell script rebuilds the site on demand. No framework.

| | |
|-|-|
| **Language** | Bash + Pandoc (apt-installable) |
| **Build time** | < 1 second |
| **Markdown support** | Via Pandoc; excellent rendering fidelity |
| **Themes** | Manual CSS only |
| **Serving** | Nginx serves static HTML directly |
| **Maintenance** | Edit `.md` → run `./build.sh` → done |

**Pros**
- Absolute minimum dependency footprint
- Nothing to learn beyond Markdown and basic HTML
- Pandoc is battle-tested at rendering complex Markdown (tables, RTL Hebrew, code blocks)

**Cons**
- CSS layout, navigation, responsiveness — all manual
- No live-preview or development server
- Photo gallery and responsive images need manual HTML coding
- Harder to achieve a polished design without significant CSS work

**Effort estimate:** ~5–8 hours (most time on CSS/layout)

---

## 4. Comparison Matrix

| Criterion | Hugo | Eleventy | Astro | Pandoc+HTML |
|-----------|------|----------|-------|-------------|
| Setup speed | Fast | Medium | Medium | Fast |
| Markdown → pages | Automatic | Automatic | Automatic | Manual script |
| Polished theme available | **Yes** | Partial | Yes | No |
| Node.js dependency | No | Yes | Yes | No |
| Responsive out of box | Theme-dependent | Manual | Theme-dependent | Manual |
| Photo gallery support | Built-in | Manual | Manual | Manual |
| Admin editing complexity | Edit MD + `hugo` | Edit MD + `npx 11ty` | Edit MD + `npm build` | Edit MD + `./build.sh` |
| Long-term maintainability | High | High | Medium-High | Medium |
| Appropriate for this content | **Yes** | Yes | Yes | Yes |

---

## 5. Recommendation

**Hugo** with the **PaperMod** theme.

**Rationale:**
- PaperMod is a widely-used, beautiful, minimal theme that renders cleanly on both mobile and large screens — exactly requirement 1 and 2. It has a dignified, uncluttered aesthetic appropriate for a memorial site.
- No Node.js build dependency (the already-installed Node.js can still be used for optional tooling, but Hugo is self-contained).
- The fastest path from "edit a Markdown file" to "website updated" — requirement 5.
- Native image processing handles the family photos (requirement: gallery page).
- The single-binary install (`apt install hugo` or a direct binary download) means the VM setup is minimal.
- For this content's lifespan (1–2 months, family audience), Hugo's maturity and zero-runtime model is the lowest-risk choice.

---

## 6. Content Architecture

Each Markdown file has a YAML front-matter block at the top (title, description, weight for menu ordering). The body is standard Markdown — same syntax as the existing research report.

```
---
title: "Timeline"
description: "Herman Freiman's journey from Boryslaw to Israel"
weight: 3
---

## Pre-War Period (1910–1941)
...
```

### Page Map

| Page | URL | Source File | Content |
|------|-----|-------------|---------|
| Home | `/` | `content/_index.md` | Brief biography, hero portrait photo |
| Biography | `/biography/` | `content/biography.md` | Sections 1 (identification, parents, marriage) |
| Timeline | `/timeline/` | `content/timeline.md` | Section 4 (all four periods) |
| Family Tree | `/family/` | `content/family.md` | Section 5 (ASCII tree) |
| Documents | `/documents/` | `content/documents.md` | Sections 7–8 (restitution, transcriptions) |
| History | `/history/` | `content/history.md` | Section 9 (Jewish Boryslaw) |
| Gallery | `/gallery/` | `content/gallery.md` | Personal/ photos with captions |
| Research | `/research/` | `content/research.md` | Sections 3, 6, 10 (discrepancies, tattoo, next steps) |

---

## 7. Deployment Architecture

```
Hetzner VM (Ubuntu 26.04)
│
├── /opt/hermanfreiman/          ← Hugo project root
│   ├── content/                 ← Markdown source files (single truth point)
│   ├── static/photos/           ← Symlink or copy of documents/Personal/
│   └── public/                  ← Hugo build output (generated HTML)
│
└── nginx
    └── /etc/nginx/sites-enabled/hermanfreiman.com
        └── root → /opt/hermanfreiman/public/
```

**Update workflow for admin:**
1. Edit a Markdown file in `content/`
2. Run `hugo` (from the project directory)  
3. Nginx immediately serves the updated `public/` directory

Optionally, a one-line shell alias can chain both steps: `alias publish='hugo && echo Done'`

---

## 8. Implementation Steps

Once this document is approved, implementation proceeds in order:

### Step 1 — Install Hugo
```bash
# Latest extended version (needed for SCSS themes like PaperMod)
apt-get install hugo
# or download binary directly if apt version is too old
```

### Step 2 — Install Nginx
```bash
apt-get install nginx certbot python3-certbot-nginx
```

### Step 3 — Scaffold Hugo Project
```bash
hugo new site /opt/hermanfreiman
cd /opt/hermanfreiman
git init
git submodule add https://github.com/adityatelange/hugo-PaperMod themes/PaperMod
```

### Step 4 — Configure Hugo (`hugo.toml`)
Set site title, base URL, theme, navigation menu, language, and PaperMod parameters (color scheme, cover image, social links).

### Step 5 — Split Markdown into Content Files
Break `documents/Herman_Freiman_Research_Report.md` into the 8 page files listed above. Add YAML front-matter to each. The original file remains untouched as the canonical source.

### Step 6 — Copy / Link Photos
Copy `documents/Personal/` images into `static/photos/` for Hugo to serve. Add a gallery page with captions.

### Step 7 — Local Preview
```bash
hugo server -D   # live-preview at http://localhost:1313
```
Review all pages, check mobile rendering, verify Hebrew text direction.

### Step 8 — Configure Nginx
Create a server block for `hermanfreiman.com` pointing to `/opt/hermanfreiman/public/`. Enable gzip and cache headers.

### Step 9 — TLS Certificate
```bash
certbot --nginx -d hermanfreiman.com
```
Certbot auto-renews via systemd timer.

### Step 10 — DNS
Point `hermanfreiman.com` A record to the Hetzner VM's public IP.

### Step 11 — Final Build & Go Live
```bash
hugo --minify
```

---

## 9. Maintenance Cheat Sheet (for admin)

| Task | Command |
|------|---------|
| Edit content | Open any `content/*.md` file, save |
| Preview locally | `cd /opt/hermanfreiman && hugo server` |
| Publish changes | `cd /opt/hermanfreiman && hugo --minify` |
| Add a photo | Copy file to `static/photos/`, add `![caption](photos/filename.jpg)` in gallery.md |
| Renew TLS (auto) | Handled by certbot systemd timer — no action needed |

---

*Document status: **DRAFT — awaiting option selection***
*Last updated: June 2026*
