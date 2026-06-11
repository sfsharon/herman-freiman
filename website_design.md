# Website Visual Design — hermanfreiman.org

**Purpose:** Visual and UX design direction for the memorial website.
For infrastructure, deployment, and build operations see [website_infrastructure.md](website_infrastructure.md).

---

## Table of Contents

- [1. Problem Diagnosis](#1-problem-diagnosis)
  - [1.1 The Narrow Column Problem](#11-the-narrow-column-problem)
  - [1.2 The Blog-Aesthetic Problem](#12-the-blog-aesthetic-problem)
  - [1.3 The Home Page Problem](#13-the-home-page-problem)
  - [1.4 Markdown Is Not the Main Problem](#14-markdown-is-not-the-main-problem)
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
  - [4.6 Future Hebrew Version](#46-future-hebrew-version)
- [5. Implementation Options](#5-implementation-options)
  - [Option A: Customize PaperMod — CSS override + custom home layout](#option-a-customize-papermod--css-override--custom-home-layout)
  - [Option B: Replace PaperMod with a Hugo custom theme](#option-b-replace-papermod-with-a-hugo-custom-theme)
  - [Option C: Migrate to Astro framework](#option-c-migrate-to-astro-framework)
  - [Option D: Pure HTML landing page, Hugo for inner pages](#option-d-pure-html-landing-page-hugo-for-inner-pages)
- [6. Implementation Phases & Progress](#6-implementation-phases--progress)

---

## 1. Problem Diagnosis

*This section documents why the original Hugo + PaperMod implementation was replaced by Astro. It is kept as context for the design decisions made. The problems described here no longer exist in the current Astro build.*

The original site worked correctly but looked like printed Markdown. Four distinct problems caused this.

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

### 1.4 Markdown Is Not the Main Problem

The current weakness does not come from using Markdown as the content source. Markdown is still a good format for biography text, document notes, historical context, captions, and research updates. It keeps the material editable and avoids mixing content with layout code.

The problem is that the Markdown is currently being rendered almost one-to-one as article content. The website needs a designed presentation layer between the source material and the visitor:

- A curated home page, not a rendered document
- Visual section entries, not only text links
- Larger image-led blocks where the photographs carry the story
- Custom layouts for special pages such as Gallery, Timeline, Documents, and Family Tree
- Wider containers and full-width page sections where appropriate

In other words: keep Markdown for the archive, but stop letting the default theme decide the experience.

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

The important lesson from USHMM is not a specific framework or technology. It is that the site behaves like a curated public-history experience rather than a document repository. The homepage gives visitors visual choices, story pathways, current/featured material, and institutional orientation before asking them to read long text.

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
| **Curated entry paths** | Visitors are guided into major areas through prominent visual modules rather than a plain list of pages. |
| **Content blocks** | The home page is assembled from designed sections: featured story, learning path, collection prompt, news/event cards, and calls to explore. |

### 3.2 What to Borrow

- Dark header bar with the name and Hebrew name in serif type
- Card-based navigation on the home page, where each card is a photograph + section title
- Generous padding and contained-width sections (not 720px columns but sensible 1100–1200px containers)
- Restrained palette: near-black, off-white, and one warm accent (parchment, sepia, or deep red)
- A homepage that acts as a gateway into the story: Biography, Timeline, Gallery, Documents, Historical Context, and Open Questions
- Occasional featured artifacts or photographs near the top of the page, so the archival material is visible before the visitor chooses a section

### 3.3 What to Avoid

- USHMM has heavy institutional navigation with many subsections. This site is small and personal — keep the nav simple.
- USHMM uses the dark header across the entire site. For a personal family memorial, a dark header on only the home page (with lighter inner pages) may feel more appropriate.
- Do not copy the institutional scale of USHMM. Herman Freiman's site should feel personal, familial, and intimate, not like a museum department.
- Do not turn the page into a marketing-style landing page. The subject needs dignity, restraint, and evidence-led storytelling.

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

The home page should replace the current text block with a designed gateway. The visitor should not feel they are reading the first Markdown file in a folder; they should feel they have entered a small curated archive.

Recommended home page order:

1. **Signature masthead** — Herman's modified signature appears as a personal visual mark at the top of the site.
2. **Human opening line** — short, concrete, and story-led.
3. **Portrait or family image** — one strong photograph above or beside the opening text.
4. **Section cards** — visual links into the main parts of the archive.
5. **Short timeline strip** — 4–5 major life milestones.
6. **Featured artifact** — one document or photograph with a caption and a link to Documents or Gallery.
7. **About this site** — a brief note that this is a family research and remembrance project.

The section-card area should use a 2×3 (or 2×4) grid. Each card represents one section of the site:

| Card | Photograph | Section |
|------|-----------|---------|
| Biography | Portrait (Herman in suit) | `/biography/` |
| Timeline | Family photo with sons | `/timeline/` |
| Gallery | Ester and Herman smiling | `/gallery/` |
| Documents | Signature image | `/documents/` |
| History | (Use a map or text card) | `/history/` |
| Family Tree | Family group photo | `/family/` |

Each card: full-width photo, title overlay at bottom, hover effect (slight zoom or dim+text). Clicking the card navigates to the section.

Above the card grid: a brief dedication — his name in large serif type (both Latin and Hebrew), birth and major life locations, and 2–3 sentences of introduction.

Suggested opening line:

> Herman Zvi Freiman was born in Boryslaw in 1910. His wife and children did not survive the Holocaust. He did.

That sentence is emotionally direct without being theatrical. It gives the visitor a reason to continue, then the site can move into the documentary record.

### 4.5 Inner Pages

Inner pages (Biography, Timeline, Gallery, etc.) have a 960px content area (set via `--content-width` in `global.css`) and do not need the full dramatic treatment of the home page. Future improvements for inner pages:

- ✅ 960px content area (already set in `global.css`)
- ✅ No blog chrome (no reading time, word count, breadcrumbs — Astro has none by default)
- ✅ Clean sans-serif body text
- Allow images to span full content width where appropriate (Phase 4)
- Tables styled with parchment stripe rows — defined in `global.css .prose table` (Phase 3+)

Some pages should eventually get custom layouts rather than generic article rendering:

| Page | Recommended treatment |
|------|-----------------------|
| Biography | Narrative article with portrait, pull quote, and source callouts |
| Timeline | Visual chronological blocks instead of plain tables only |
| Gallery | Image-first grid with captions that identify people, place, and approximate year |
| Documents | Archival cards with source, date, document type, transcription, and translation |
| Family Tree | Designed tree or relationship layout instead of ASCII text |
| Research Notes | Consider renaming to **Open Questions** or **Unresolved Questions** |

### 4.6 Future Hebrew Version

The site should eventually support a full Hebrew version, not just isolated Hebrew names or phrases. This does not need to be implemented in the first redesign phase, but the design and technical choices should allow for it.

Future Hebrew support means:

- A complete Hebrew translation of every page
- Right-to-left page direction for Hebrew pages
- Hebrew navigation labels
- Hebrew typography that feels intentional, not like a fallback font
- Correct layout mirroring where appropriate: navigation, timelines, captions, document metadata, and page-level reading flow
- A visible language switcher between English and Hebrew
- Separate URLs for each language, for example `/en/biography/` and `/he/biography/`, or English at `/biography/` and Hebrew at `/he/biography/`

Design implications:

- Avoid layouts that only work left-to-right.
- Avoid embedding English text directly into templates where it cannot be translated.
- Keep card titles, navigation labels, button text, and section headings data-driven where possible.
- Choose font stacks with high-quality Hebrew support. For Hebrew headings, `Noto Serif Hebrew`, `David Libre`, or another purpose-selected Hebrew serif should be tested visually.
- Test mixed English/Hebrew text carefully, especially names, dates, archive identifiers, and document citations.

Technology implications:

- Astro supports multilingual routing natively, so future Hebrew support does not require a framework change.
- The important decision is to keep content and labels structured enough that English and Hebrew versions can diverge naturally where translation requires different sentence order, page rhythm, or caption length.
- Navigation labels, card titles, and button text in Astro components should be kept as variables or props rather than hardcoded strings, so they can be swapped per language.

---

## 5. Implementation Options

*These options were evaluated before the Astro migration. Option C was selected and implemented. Options A, B, and D are kept here as historical context.*

---

### Option A: Customize PaperMod — CSS override + custom home layout *(not taken)*

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

### Option B: Replace PaperMod with a Hugo custom theme *(not taken)*

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

### Option C: Migrate to Astro framework *(selected — implemented June 2026)*

**What it means:** Replace Hugo with [Astro](https://astro.build/), a modern static site generator designed for content-heavy, visually rich sites.

**What was actually built (Phase 1):**
Pages are `.astro` component files (HTML + scoped logic). The existing `content/*.md` files from Hugo are **not yet connected** — Phase 1 created stub pages with placeholder text. The Markdown content will be migrated into the Astro pages in Phase 3. Styling is done with plain CSS (`src/styles/global.css`) using CSS custom properties for the design tokens — no CSS framework was added. The build output is a `dist/` folder of static HTML that nginx serves identically to before.

**Build command:** `npm run build` (replaces `hugo --minify`)
**Dev server:** `npm run dev` (port 8080, replaces `hugo server`)

**Pros:**
- Full HTML/CSS control over every page — no theme constraints
- Component model (`Header.astro`, `BaseLayout.astro`, etc.) keeps design consistent across pages
- The `content/*.md` files can be imported as Astro content collections in Phase 3, preserving the edit-Markdown workflow
- Good fit for future Hebrew support through language-specific routes and shared bilingual components

**Cons:**
- Node.js required (already present on the VM)
- The `content/*.md` files need Hugo shortcodes replaced before they can be used in Astro (Phase 3 work)
- Build pipeline changed — infrastructure document updated to reflect this
- Significantly more complex than Hugo for what remains a small, static site

---

### Option D: Pure HTML landing page, Hugo for inner pages *(not taken)*

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

## 6. Implementation Phases & Progress

**Astro was chosen as the framework.** See §5 Option C for the rationale.

The current site needs a stronger presentation layer than PaperMod can comfortably provide. The problem is still not Markdown itself; the problem is that Hugo + PaperMod is rendering the content too much like article pages. Astro keeps the useful parts of a static content workflow while making it much easier to build a designed, image-led, component-based memorial site.

Astro is the better direction for this project because:

- The homepage can be built as a real visual composition, not as a themed Markdown page.
- Components such as `Hero`, `SignatureHeader`, `SectionCard`, `TimelineStrip`, `ArtifactCard`, and `LanguageSwitcher` can be reused across the site.
- Markdown or MDX can still be used for long-form content, captions, source notes, and document descriptions.
- Future Hebrew support can be planned from the start with localized routes, RTL layouts, translated navigation, and shared bilingual components.
- The final output is still static HTML/CSS/JS, so nginx deployment remains conceptually simple.

This is the recommended sequence:

1. **Phase 1 — Astro scaffold** ✅ *Completed 2026-06-10*
   - Astro v4 project with `package.json`, `astro.config.mjs`, `tsconfig.json`
   - `src/layouts/BaseLayout.astro` — HTML shell, fonts, meta
   - `src/components/Header.astro` — dark header with signature watermark, active-state nav
   - `src/components/Footer.astro` — archive credit and sources line
   - `src/styles/global.css` — full design token set (palette, typography, component styles)
   - `src/pages/index.astro` — dedication + section-card grid stub
   - `src/pages/{biography,timeline,family,gallery,documents,history,research}.astro` — stubs
   - Photos moved from `static/photos/` → `public/photos/` (Astro's static assets dir)
   - Hugo removed: `hugo.toml`, `themes/PaperMod` submodule, `static/` dir deleted
   - nginx root updated from `public/` → `dist/` (Astro's build output dir)
   - Build command: `npm run build` (was `hugo --minify`)
   - Dev command: `npm run dev` (binds 0.0.0.0:8080, accessible via existing SSH tunnel)

2. **Phase 2 — Visual homepage** ✅ *Completed 2026-06-11*
   - Split header: light identity zone (signature at original colors) + dark nav bar
   - Hero section: portrait + dedication text with opening quote
   - Photo section cards (6 cards with real photos and gradient overlays)
   - Timeline strip (5 key milestones, 1910–1950s)
   - About this archive section
3. **Phase 3 — Content migration** ✅ *Completed 2026-06-11*
   - All content migrated from `content/*.md` into `src/pages/*.astro`
   - Hugo `{{< figure >}}` shortcodes replaced with standard HTML `<figure>` elements
   - `content/` directory deleted; `.gitmodules` and `.hugo_build.lock` removed
4. **Phase 4 — Custom page layouts** ✅ *Completed 2026-06-11*
   - **Biography:** portrait-led header (photo + identification table side by side), pull quote
   - **Timeline:** visual event list with year badges, accent border, period callout notes
   - **Documents:** archival cards per document (navy header, parchment body, German/English columns)
   - **Family Tree:** family photograph above ASCII tree, styled monospace tree block
   - **History:** key-facts stats bar (population numbers, dates) above narrative prose
   - **Research Notes:** styled conclusion box for tattoo question; archive links
   - **Gallery:** sectioned image grid with captions (completed in Phase 3)
5. **Phase 5 — Future Hebrew readiness:** Structure routes, components, labels, and typography so a full Hebrew version can be added later without redesigning the site.

Option A (Hugo + PaperMod CSS override) is no longer relevant — the migration to Astro has been made.
