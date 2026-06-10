# Website Visual Design — hermanfreiman.org

**Purpose:** Visual and UX design direction for the memorial website.
For infrastructure, deployment, and build operations see [website_infrastructure.md](website_infrastructure.md).

---

## Table of Contents

- [1. Problem Diagnosis](#1-problem-diagnosis)
  - [1.1 The Narrow Column Problem](#11-the-narrow-column-problem)
  - [1.2 The Blog-Aesthetic Problem](#12-the-blog-aesthetic-problem)
  - [1.3 The Home Page Problem](#13-the-home-page-problem)
- [2. Design Vision](#2-design-vision)
  - [2.1 The Feeling We Want](#21-the-feeling-we-want)
  - [2.2 Key Visual Elements](#22-key-visual-elements)
- [3. Reference: USHMM Design Language](#3-reference-ushmm-design-language)
  - [3.1 What Makes USHMM Work](#31-what-makes-ushmm-work)
  - [3.2 What to Borrow](#32-what-to-borrow)
  - [3.3 What to Avoid](#33-what-to-avoid)
- [4. Design Elements for This Site](#4-design-elements-for-this-site)
  - [4.1 The Signature Element](#41-the-signature-element)
  - [4.2 Color Palette](#42-color-palette)
  - [4.3 Typography](#43-typography)
  - [4.4 Photo Card Grid (Home Page)](#44-photo-card-grid-home-page)
  - [4.5 Inner Pages](#45-inner-pages)
- [5. Implementation Options](#5-implementation-options)
  - [Option A: Customize PaperMod — CSS override + custom home layout](#option-a-customize-papermod--css-override--custom-home-layout)
  - [Option B: Replace PaperMod with a Hugo custom theme](#option-b-replace-papermod-with-a-hugo-custom-theme)
  - [Option C: Migrate to Astro framework](#option-c-migrate-to-astro-framework)
  - [Option D: Pure HTML landing page, Hugo for inner pages](#option-d-pure-html-landing-page-hugo-for-inner-pages)
- [6. Recommendation](#6-recommendation)
- [7. Implementing the Recommendation (Option A)](#7-implementing-the-recommendation-option-a)
  - [7.1 Step 1 — Widen the content area](#71-step-1--widen-the-content-area)
  - [7.2 Step 2 — Create the signature header](#72-step-2--create-the-signature-header)
  - [7.3 Step 3 — Replace the home page with a photo-card grid](#73-step-3--replace-the-home-page-with-a-photo-card-grid)
  - [7.4 Step 4 — Refine inner page styling](#74-step-4--refine-inner-page-styling)
  - [7.5 Rebuild and test](#75-rebuild-and-test)

---

## 1. Problem Diagnosis

The site works correctly but it looks like printed Markdown. Three distinct problems cause this.

### 1.1 The Narrow Column Problem

PaperMod's core layout CSS sets:

```css
--main-width: 720px;
```

Every page — including the home page — is capped at 720 pixels. On a modern desktop (1440px wide) that leaves 35% blank whitespace on each side. This makes the site feel like a Word document centred on a large screen, not a website that occupies and owns the page.

### 1.2 The Blog-Aesthetic Problem

PaperMod is a blog theme. It is beautifully designed for one thing: displaying long-form text articles with clean typography. That is not what this site is. This site is a memorial — it has photos, documents, historical narrative, tables, and an emotional story to tell. The blog aesthetic — reading time, breadcrumbs, post-nav "prev/next", list-style home page — all signal "software documentation" rather than "this was a real person".

### 1.3 The Home Page Problem

The current home page is PaperMod's default "profile mode" — a centred text block with a cover image above it. This is the standard blog landing page. For a memorial, the home page is the most important page: it should immediately convey who Herman was through photographs, create a feeling of solemnity and weight, and invite the visitor to explore.

Currently it does none of these.

---

## 2. Design Vision

### 2.1 The Feeling We Want

A visitor arriving at hermanfreiman.org should feel they have arrived somewhere significant. Before they read a single word they should sense:

- This is about a real person
- This person lived through extraordinary history
- This is a curated archive, not a blog

The emotional register is quiet, serious, respectful — not dramatic or graphic. Light use of whitespace. Real photographs given proper space. Text that serves the photographs, not the other way around.

### 2.2 Key Visual Elements

**The signature** (`Herman_Fraiman_Signature_Modified.png`) is the most personal object in the entire archive. A man's handwriting is uniquely his. Used correctly, the signature alone transforms the site from "information about someone" to "this person was here".

**The portrait** (`personal_Zvi_Fraiman_With_Suite_Age_40_Approx.png`) — Herman in a suit, circa age 40, looking directly at the camera. This is the anchor image for the whole site.

**The family photographs** — Ester and Herman together, the family photo with their sons — show the life that was rebuilt after the Holocaust. These are the images of survival.

---

## 3. Reference: USHMM Design Language

The United States Holocaust Memorial Museum website (ushmm.org) is the most direct reference for the aesthetic we want.

### 3.1 What Makes USHMM Work

| Element | What they do |
|---------|-------------|
| **Header** | Dark background (#1a1a1a or equivalent), white text, institutional gravitas. The dark header immediately tells the visitor this is not a casual website. |
| **Full-width sections** | The page is not constrained to a single narrow column. Cards and sections stretch to the full container width. |
| **Photo cards** | Navigation sections are represented as photo cards — a photograph, a short title, a brief description. The photo does the communicating, not the text. |
| **Whitespace** | Generous padding around everything. The whitespace creates breathing room for difficult content. |
| **Typography** | Serif headlines (weight and dignity), sans-serif body text (readability). |
| **Color restraint** | Near-black, white, and one muted accent color. No decorative gradients, no cheerful colors. |
| **Historical photographs** | Photos are given real estate. They are not small thumbnails — they are allowed to be large and visible. |

### 3.2 What to Borrow

- Dark header bar with the name and Hebrew name in serif type
- Card-based navigation on the home page, where each card is a photograph + section title
- Generous padding and contained-width sections (not 720px columns but sensible 1100–1200px containers)
- Restrained palette: near-black, off-white, and one warm accent (parchment, sepia, or deep red)

### 3.3 What to Avoid

- USHMM has heavy institutional navigation with many subsections. This site is small and personal — keep the nav simple.
- USHMM uses the dark header across the entire site. For a personal family memorial, a dark header on only the home page (with lighter inner pages) may feel more appropriate.

---

## 4. Design Elements for This Site

### 4.1 The Signature Element

`Herman_Fraiman_Signature_Modified.png` should appear in the site header. Two uses are possible:

**Option A — Header watermark:** The signature appears as a large, very-low-opacity watermark behind the site title text. It occupies the full width of the header band, serving as a background texture. Effect: immediately personal and intimate.

**Option B — Standalone element:** The signature appears to the left of or below the site title, at full opacity but smaller. Effect: more restrained, like a seal or monogram.

Recommendation: Option A (watermark). The low-opacity watermark gives the header depth and personality without competing with the name text.

### 4.2 Color Palette

| Name | Hex | Use |
|------|-----|-----|
| Deep charcoal | `#1c1c1e` | Header background, section titles |
| Off-white | `#f8f5f0` | Page background (warm, not harsh white) |
| Parchment | `#e8dcc8` | Card backgrounds, table backgrounds |
| Warm mid-gray | `#6b6560` | Body text, captions |
| Accent: dark sepia | `#7a5c3a` | Links, hover states, borders |

This palette echoes aged documents and photographs — appropriate for archival material from the 1940s–50s.

### 4.3 Typography

| Use | Font | Why |
|-----|------|-----|
| Site title / Herman's name | Serif (EB Garamond, Playfair Display, or similar) | Weight and history |
| Section headings (h1, h2) | Same serif, bold weight | Consistency |
| Body text | Sans-serif (Source Sans Pro, Lato) | Readability for long biographical text |
| Hebrew text | System Hebrew stack (`'Noto Serif Hebrew', 'David Libre', serif`) | Proper RTL rendering |
| Dates, labels, captions | Monospace or small-caps sans-serif | Archive/document feel |

All fonts are available free from Google Fonts and can be self-hosted (no external requests).

### 4.4 Photo Card Grid (Home Page)

The home page should replace the current text block with a 2×3 (or 2×4) grid of cards. Each card represents one section of the site:

| Card | Photograph | Section |
|------|-----------|---------|
| Biography | Portrait (Herman in suit) | `/biography/` |
| Timeline | Family photo with sons | `/timeline/` |
| Gallery | Ester and Herman smiling | `/gallery/` |
| Documents | Signature image | `/documents/` |
| History | (Use a map or text card) | `/history/` |
| Family Tree | Family group photo | `/family/` |

Each card: full-width photo, title overlay at bottom, hover effect (slight zoom or dim+text). Clicking the card navigates to the section.

Above the card grid: a brief dedication — his name in large serif type (both Latin and Hebrew), birth–death years, and 2–3 sentences of introduction.

### 4.5 Inner Pages

Inner pages (Biography, Timeline, Gallery, etc.) benefit from a wider content area than PaperMod's 720px default, but they do not need the full dramatic treatment of the home page. Recommended changes for inner pages:

- Widen to 960–1100px content area
- Remove the "reading time", "word count", breadcrumb clutter
- Keep the clean sans-serif body text
- Allow images to span full content width (not constrained to inline)
- Tables styled with parchment stripe rows instead of default gray

---

## 5. Implementation Options

Four approaches are available, ranging from minimal change to full replacement.

---

### Option A: Customize PaperMod — CSS override + custom home layout

**What it means:** Keep Hugo and PaperMod. Override PaperMod's CSS via Hugo's custom CSS extension point (`assets/css/extended/custom.css`). Replace only the home page template with a custom `layouts/index.html`.

**How it works:**
- Hugo looks for `layouts/index.html` in the site's own `layouts/` folder before looking in the theme. You create one file and PaperMod's home template is bypassed entirely.
- A `assets/css/extended/custom.css` file is automatically picked up by PaperMod and appended to its compiled CSS. You override any variable or class here.
- All other pages (Biography, Timeline, etc.) remain PaperMod-rendered but can be widened and restyled through the same CSS file.
- The Markdown source files are completely unchanged.

**Effort:** 1–2 days of HTML + CSS work. No migration, no new tools.

**What you keep:** Everything — nginx, certbot, Hugo build workflow, all Markdown content, git history.

**Pros:**
- Lowest risk — inner pages continue to work exactly as they do now
- The Markdown-to-HTML workflow that already works is preserved
- The content pages (Biography, Timeline) actually look fine for document-style content; only the home page needs dramatic change
- No new software dependencies

**Cons:**
- You are still partially constrained by PaperMod's HTML structure on inner pages (though CSS can override most visual aspects)
- If PaperMod releases a breaking theme update, the custom files must be reconciled

---

### Option B: Replace PaperMod with a Hugo custom theme

**What it means:** Remove PaperMod. Write a minimal Hugo theme from scratch in `themes/herman/` with full HTML control over every page.

**How it works:**
Hugo's templating system builds pages from `layouts/` files. A theme is just a set of these layout files. You write `baseof.html` (site shell), `index.html` (home page), `single.html` (content pages), and a CSS file.

**Effort:** 3–5 days. Requires writing every layout file, and the inner pages lose PaperMod's polished typography until you style them.

**Pros:**
- Total design freedom
- No third-party theme to fight

**Cons:**
- Significant work to recreate everything PaperMod gives for free (navigation, TOC, code blocks, mobile responsiveness, table styling)
- High risk of half-finished pages while iterating

---

### Option C: Migrate to Astro framework

**What it means:** Replace Hugo with [Astro](https://astro.build/), a modern static site generator designed for content-heavy, visually rich sites.

**How it works:**
Astro reads the existing `.md` files directly (with minor frontmatter adjustments). Pages are built from `.astro` component files which are HTML + JavaScript components. You can use any CSS framework (Tailwind CSS is the most common). The build output is the same — a `dist/` folder of static HTML that nginx serves identically.

**Effort:** 1–2 weeks to migrate. Requires learning Astro's template syntax (simpler than React). Hugo build command (`hugo --minify`) is replaced by `npx astro build`.

**Pros:**
- Purpose-built for visually rich content sites
- Component model (Header, Card, HeroSection) makes the design coherent and maintainable
- Tailwind CSS makes the USHMM-style design achievable in hours
- Large ecosystem of visual themes and components

**Cons:**
- Requires Node.js (install on the VM: `apt install nodejs npm`)
- Learning curve for Astro templates
- The entire build pipeline changes — the infrastructure document must be rewritten for the new build command
- `.md` frontmatter may need adjustments (Hugo's `{{< figure >}}` shortcodes do not exist in Astro)
- Significantly more complex than Hugo for what remains a small, static site

---

### Option D: Pure HTML landing page, Hugo for inner pages

**What it means:** Write the home page (`public/index.html`) as a standalone HTML + CSS file, hand-crafted. Hugo continues to serve all inner pages unchanged.

**How it works:**
Hugo does not generate `index.html` from `content/_index.md` if you place a static `index.html` directly in `static/`. This single file contains the full home page design: the signature watermark header, the photo card grid, the dedication text. Inner pages remain PaperMod-rendered.

**Effort:** 1 day of HTML + CSS. The simplest possible approach for the home page redesign.

**Pros:**
- No build tooling changes whatsoever
- The home page can look exactly like USHMM — full design freedom for that one file
- Inner pages (which are actually fine as documents) are untouched
- Zero learning curve if you can write HTML and CSS

**Cons:**
- The home page lives outside the Hugo build system — navigation and links must be maintained manually in the HTML file
- No reuse of the site header/footer across home and inner pages; they will look different
- Two separate maintenance workflows (edit HTML vs. edit Markdown)

---

## 6. Recommendation

**Start with Option A.**

The home page is the highest-impact change and Option A delivers it with the lowest risk. The inner pages already serve their purpose — the Biography page with its identification table, the Timeline with its chronological tables — these are document-style pages and document-style layout is appropriate. The problem is specifically the home page and the narrow column.

With Option A you can achieve the full USHMM-inspired design:
- Custom home page with signature watermark header, full-width photo cards, proper typography
- Widened inner pages (960px instead of 720px)
- The sepia/parchment color palette applied site-wide
- No migration, no new tools, no risk to the working infrastructure

If after implementing Option A the inner pages still feel unsatisfying and you want total design control, migrating to Astro (Option C) remains on the table — and by then you will have a clear picture of exactly what design you want, which makes the Astro migration faster.

---

## 7. Implementing the Recommendation (Option A)

The following steps implement the design vision using Hugo + PaperMod customization.

### 7.1 Step 1 — Widen the content area

Create the file `assets/css/extended/custom.css`. Hugo + PaperMod will automatically include it.

```css
/* Widen content area from PaperMod's default 720px */
:root {
    --main-width: 960px;
}

/* Warm off-white page background */
body {
    background-color: #f8f5f0;
}

/* Sepia accent color for links */
a {
    color: #7a5c3a;
}
a:hover {
    color: #1c1c1e;
}

/* Tables: parchment stripe */
table tr:nth-child(even) {
    background-color: #e8dcc8;
}

/* Wider images on content pages */
.post-content img {
    max-width: 100%;
}
```

### 7.2 Step 2 — Create the signature header

The site header needs to contain the signature as a watermark. In PaperMod, the header is rendered by `themes/PaperMod/layouts/partials/header.html`. You override it by creating `layouts/partials/header.html` in the site root.

The custom header:

```html
<!-- layouts/partials/header.html -->
<header class="site-header">
  <div class="header-inner">
    <img
      class="signature-watermark"
      src="/photos/Herman_Fraiman_Signature_Modified.png"
      alt=""
      aria-hidden="true"
    />
    <div class="header-text">
      <a href="{{ .Site.BaseURL }}" class="site-title">
        Herman Zvi Freiman
      </a>
      <span class="site-title-hebrew" dir="rtl">הרמן צבי פריימן</span>
      <span class="site-dates">1910 – Boryslaw, Galicia</span>
    </div>
    <nav class="site-nav">
      {{- range .Site.Menus.main }}
      <a href="{{ .URL }}">{{ .Name }}</a>
      {{- end }}
    </nav>
  </div>
</header>
```

CSS for the header (add to `custom.css`):

```css
.site-header {
    background-color: #1c1c1e;
    position: relative;
    overflow: hidden;
    padding: 2rem 0;
}

.header-inner {
    max-width: var(--main-width);
    margin: 0 auto;
    padding: 0 1.5rem;
    position: relative;
    z-index: 2;
}

.signature-watermark {
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    width: 80%;
    max-width: 700px;
    opacity: 0.08;   /* very low opacity — texture, not distraction */
    filter: invert(1);
    pointer-events: none;
    z-index: 1;
}

.site-title {
    display: block;
    font-family: 'EB Garamond', 'Georgia', serif;
    font-size: 2rem;
    font-weight: 700;
    color: #f8f5f0;
    text-decoration: none;
    letter-spacing: 0.04em;
}

.site-title-hebrew {
    display: block;
    font-family: 'Noto Serif Hebrew', 'David Libre', serif;
    font-size: 1.2rem;
    color: #c8b8a0;
    margin-top: 0.25rem;
}

.site-dates {
    display: block;
    font-size: 0.85rem;
    color: #8a7a68;
    margin-top: 0.25rem;
    letter-spacing: 0.08em;
    text-transform: uppercase;
}

.site-nav {
    margin-top: 1.25rem;
    display: flex;
    flex-wrap: wrap;
    gap: 0.25rem 1.5rem;
}

.site-nav a {
    color: #c8b8a0;
    text-decoration: none;
    font-size: 0.9rem;
    letter-spacing: 0.05em;
    text-transform: uppercase;
}

.site-nav a:hover {
    color: #f8f5f0;
}
```

### 7.3 Step 3 — Replace the home page with a photo-card grid

Create `layouts/index.html`. This entirely replaces PaperMod's home page template.

```html
<!-- layouts/index.html -->
{{- define "main" }}
<div class="home-page">

  <!-- Dedication -->
  <section class="dedication">
    <h1>Herman Zvi Freiman</h1>
    <p class="dates">March 2, 1910, Boryslaw — Kfar Ata, Israel</p>
    <p class="lead">
      Holocaust survivor. Tailor. Son of Feibel and Shifra.
      Husband of Ester. Father of three sons.
      This archive documents what is known of his life,
      compiled from the Arolsen Archives, Yad Vashem, and personal family photographs.
    </p>
  </section>

  <!-- Photo card grid -->
  <section class="section-cards">
    <a href="/biography/" class="section-card">
      <img src="/photos/personal_Zvi_Fraiman_With_Suite_Age_40_Approx.png"
           alt="Herman Freiman in suit, circa 1950s" />
      <div class="card-label">
        <span class="card-title">Biography</span>
        <span class="card-sub">Identity, family, and documentary sources</span>
      </div>
    </a>

    <a href="/gallery/" class="section-card">
      <img src="/photos/personal_Ester_and_Zvi_Fraiman_Smiling.jpg"
           alt="Herman and Ester Freiman" />
      <div class="card-label">
        <span class="card-title">Gallery</span>
        <span class="card-sub">Family photographs from Israel</span>
      </div>
    </a>

    <a href="/timeline/" class="section-card">
      <img src="/photos/personal_Family_Photo_Fraiman_Side.jpg"
           alt="Freiman family" />
      <div class="card-label">
        <span class="card-title">Timeline</span>
        <span class="card-sub">Chronological record, 1910–1960s</span>
      </div>
    </a>

    <a href="/documents/" class="section-card">
      <img src="/photos/Herman_Fraiman_Signature_Modified.png"
           alt="Herman Freiman signature" />
      <div class="card-label">
        <span class="card-title">Documents</span>
        <span class="card-sub">Arolsen Archives, ITS cards, Vollmacht</span>
      </div>
    </a>

    <a href="/history/" class="section-card section-card--text">
      <div class="card-label card-label--full">
        <span class="card-title">History</span>
        <span class="card-sub">Boryslaw, the Ghetto, and the Jewish community of eastern Galicia</span>
      </div>
    </a>

    <a href="/family/" class="section-card">
      <img src="/photos/personal_Fraiman_Familiy_Photos.jpg"
           alt="Freiman family photographs" />
      <div class="card-label">
        <span class="card-title">Family Tree</span>
        <span class="card-sub">Known relatives and name variants</span>
      </div>
    </a>
  </section>

</div>
{{- end }}
```

CSS for the home page (add to `custom.css`):

```css
.home-page {
    max-width: var(--main-width);
    margin: 0 auto;
    padding: 3rem 1.5rem;
}

.dedication {
    text-align: center;
    margin-bottom: 3rem;
    padding-bottom: 2.5rem;
    border-bottom: 1px solid #d0c4b0;
}

.dedication h1 {
    font-family: 'EB Garamond', 'Georgia', serif;
    font-size: 2.4rem;
    color: #1c1c1e;
    margin-bottom: 0.4rem;
}

.dedication .dates {
    font-size: 0.9rem;
    color: #8a7a68;
    letter-spacing: 0.1em;
    text-transform: uppercase;
    margin-bottom: 1.2rem;
}

.dedication .lead {
    font-size: 1.1rem;
    color: #4a4040;
    max-width: 680px;
    margin: 0 auto;
    line-height: 1.8;
}

.section-cards {
    display: grid;
    grid-template-columns: repeat(2, 1fr);
    gap: 1.5rem;
}

@media (max-width: 640px) {
    .section-cards {
        grid-template-columns: 1fr;
    }
}

.section-card {
    display: block;
    text-decoration: none;
    background: #e8dcc8;
    border-radius: 4px;
    overflow: hidden;
    position: relative;
    aspect-ratio: 4 / 3;
    transition: transform 0.2s ease;
}

.section-card:hover {
    transform: translateY(-3px);
    box-shadow: 0 8px 24px rgba(0,0,0,0.18);
}

.section-card img {
    width: 100%;
    height: 100%;
    object-fit: cover;
    display: block;
    filter: sepia(20%) contrast(95%);
}

.card-label {
    position: absolute;
    bottom: 0;
    left: 0;
    right: 0;
    padding: 1rem;
    background: linear-gradient(transparent, rgba(28,28,30,0.85));
}

.card-title {
    display: block;
    font-family: 'EB Garamond', 'Georgia', serif;
    font-size: 1.25rem;
    font-weight: 700;
    color: #f8f5f0;
    letter-spacing: 0.03em;
}

.card-sub {
    display: block;
    font-size: 0.78rem;
    color: #c8b8a0;
    margin-top: 0.2rem;
    letter-spacing: 0.02em;
}

/* History card has no photo — use dark background */
.section-card--text {
    background: #1c1c1e;
    aspect-ratio: 4 / 3;
}

.card-label--full {
    top: 0;
    display: flex;
    flex-direction: column;
    justify-content: center;
    background: none;
    padding: 2rem;
}

.card-label--full .card-title {
    font-size: 1.6rem;
}

.card-label--full .card-sub {
    font-size: 0.9rem;
    margin-top: 0.6rem;
    line-height: 1.5;
}
```

### 7.4 Step 4 — Refine inner page styling

Add to `custom.css` to clean up the blog-oriented chrome on inner pages:

```css
/* Hide blog-oriented elements that don't belong on a memorial site */
.post-meta,
.post-footer,
.share-buttons,
.paginav {
    display: none;
}

/* Serif headings on content pages */
.post-title,
.post-content h1,
.post-content h2,
.post-content h3 {
    font-family: 'EB Garamond', 'Georgia', serif;
    color: #1c1c1e;
}

/* Breathing room between content sections */
.post-content h2 {
    margin-top: 2.5rem;
    padding-bottom: 0.4rem;
    border-bottom: 1px solid #d0c4b0;
}
```

### 7.5 Rebuild and test

After creating the three files (`assets/css/extended/custom.css`, `layouts/partials/header.html`, `layouts/index.html`):

```bash
cd /root/workspace/herman-freiman
hugo --minify
```

To preview locally before committing:

```bash
hugo server --bind 0.0.0.0 --port 8080 --baseURL http://localhost:8080
```

Then open `http://localhost:8080` in a browser (via the SSH tunnel already established on port 8080).

To load the EB Garamond font, add to `layouts/partials/head.html` (another PaperMod override):

```html
<!-- layouts/partials/head.html -->
{{- partial "head.html" (dict "Site" .Site "Params" .Params "IsHome" .IsHome "Title" .Title) }}
<link rel="preconnect" href="https://fonts.googleapis.com">
<link href="https://fonts.googleapis.com/css2?family=EB+Garamond:ital,wght@0,400;0,700;1,400&display=swap" rel="stylesheet">
```

Note: if you prefer to avoid external font requests, download the font files and serve them from `static/fonts/` with a `@font-face` rule in `custom.css`.
