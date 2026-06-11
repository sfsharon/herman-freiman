# Website Infrastructure & Implementation Plan
## hermanfreiman.org

*Herman (Zvi) Freiman — Holocaust Survival Research*

> **Note:** This document covers infrastructure, deployment, and build operations.
> For visual and UX design direction, see [website_design.md](website_design.md).

---

## Table of Contents

- [1. Requirements Summary](#1-requirements-summary)
- [2. Proposed Site Structure](#2-proposed-site-structure)
- [3. Technology Options](#3-technology-options)
  - [Option A — Hugo (initial choice, superseded)](#option-a--hugo-initial-choice-superseded)
  - [Option B — Eleventy (11ty)](#option-b--eleventy-11ty-not-selected)
  - [Option C — Astro (current)](#option-c--astro-current)
  - [Option D — Plain HTML + Pandoc](#option-d--plain-html--pandoc-not-selected)
- [4. Comparison Matrix](#4-comparison-matrix)
- [5. Selected Option: Astro](#5-selected-option-astro)
- [6. Content Architecture](#6-content-architecture)
  - [Page Map](#page-map)
- [7. Security & Exposure](#7-security--exposure)
  - [7.1 Do I need TLS (HTTPS)?](#71-do-i-need-tls-https)
  - [7.2 Is opening port 80 a security risk?](#72-is-opening-port-80-a-security-risk)
- [8. Deployment Architecture](#8-deployment-architecture)
- [9. Implementation Steps](#9-implementation-steps)
  - [Step 1 — Verify Node.js](#step-1--verify-nodejs)
  - [Step 2 — Install Nginx](#step-2--install-nginx)
  - [Step 3 — Install Astro dependencies](#step-3--install-astro-dependencies)
  - [Step 4 — Astro Configuration](#step-4--astro-configuration)
  - [Step 5 — Source Files](#step-5--source-files-already-done)
  - [Step 6 — Local Preview](#step-6--local-preview)
  - [Step 7 — Initial Build](#step-7--initial-build)
  - [Step 8 — Configure Nginx](#step-8--configure-nginx)
  - [Step 9 — VM Firewall](#step-9--vm-firewall)
  - [Step 10 — DNS](#step-10--dns)
  - [Step 11 — TLS Certificate](#step-11--tls-certificate-optional-but-recommended)
- [10. How to Start the Website](#10-how-to-start-the-website)
- [11. Updating Content (day-to-day workflow)](#11-updating-content-day-to-day-workflow)
  - [Step 1 — Edit the Markdown file](#step-1--edit-the-markdown-file)
  - [Step 2 — Rebuild the site](#step-2--rebuild-the-site)
  - [Why the different build commands?](#why-the-different-build-commands)
  - [Step 3 — Verify](#step-3--verify)
- [12. Maintenance Cheat Sheet](#12-maintenance-cheat-sheet)
- [13. Go-Live Checklist (DNS + TLS)](#13-go-live-checklist-dns--tls)
  - [A — Set DNS records](#a--set-dns-records-cloudflare-dashboard)
  - [B — Build the Astro site](#b--build-the-astro-site)
  - [C — Run Certbot](#c--run-certbot-get-tls-certificate)
  - [D — Verify HTTPS](#d--verify-https)
  - [E — Verify](#e--verify)

---

## 1. Requirements Summary

| # | Requirement | Notes |
|---|-------------|-------|
| 1 | Clean, readable layout | Not cluttered; dignified, memorial-appropriate design |
| 2 | Fully responsive | Mobile phones through large desktop monitors |
| 3 | No database | Static content only, no server-side runtime needed |
| 4 | Single truth point | Markdown file(s) as the sole content source |
| 5 | Easy to maintain | Admin can update content by editing Markdown files |
| 6 | Deploy on Hetzner VM | Ubuntu 26.04 LTS; Node.js v22, Python 3.14 already present |
| 7 | Low traffic, temporary | A handful of family members; site may run for 1–2 months |

---

## 2. Proposed Site Structure

The monolithic `Herman_Freiman_Research_Report.md` is split into dedicated per-page Markdown files. Each file maps to one website page and is independently editable.

```
content/
├── _index.md       # Home — brief biography + hero portrait
├── biography.md    # Sections 1–2 (identification, parents, marriage, sources)
├── timeline.md     # Section 4 (all four chronological periods)
├── family.md       # Section 5 (ASCII family tree)
├── documents.md    # Sections 7–8 (restitution documents, transcriptions)
├── history.md      # Section 9 (historical context: Jewish Boryslaw)
├── gallery.md      # Photo gallery (Personal/ images with captions)
└── research.md     # Sections 3, 6, 10 (discrepancies, tattoo, next steps)
```

Editing a page = opening one Markdown file and saving. No code changes needed.

---

## 3. Technology Options

### Option A — Hugo *(initial choice, superseded)*

Hugo with the PaperMod theme was the original implementation. It was replaced by Astro in June 2026 because PaperMod's document-oriented layout and 720px content width could not deliver the memorial-site visual design without extensive fighting against the theme. See `website_design.md` §1 for the full diagnosis.

| | |
|-|-|
| **Language** | Go (single self-contained binary) |
| **Build time** | < 1 second |
| **Status** | **Removed** — `hugo.toml` and `themes/PaperMod` deleted |

---

### Option B — Eleventy (11ty) *(not selected)*

Node.js SSG, highly flexible, mostly DIY CSS/layout. ~4–6 hours of setup effort due to no ready-made theme that suits this content.

### Option C — Astro *(current)*

Modern Node.js static site generator, content-first, zero JS shipped to browser by default. Selected in June 2026 as the replacement for Hugo. See §5 for rationale.

### Option D — Plain HTML + Pandoc *(not selected)*

Absolute minimal dependencies but requires all CSS/layout work by hand. ~5–8 hours effort.

---

## 4. Comparison Matrix

| Criterion | Hugo (removed) | Eleventy | **Astro (current)** | Pandoc+HTML |
|-----------|----------|----------|-------|-------------|
| Setup speed | Fast | Medium | **Medium** | Fast |
| Markdown → pages | Automatic | Automatic | **Automatic** | Manual script |
| Polished theme available | Yes (PaperMod) | Partial | **Custom (built)** | No |
| Node.js dependency | No | Yes | **Yes** | No |
| Responsive out of box | Yes | Manual | **Yes (custom CSS)** | Manual |
| Full design control | Limited | High | **High** | Full |
| Admin editing | Edit MD + hugo | Edit MD + npx | **Edit .astro + npm run build** | Edit MD + script |
| Long-term maintainability | High | High | **High** | Medium |

---

## 5. Selected Option: Astro

**Astro** (v4) is the current framework. It replaced Hugo + PaperMod in June 2026. Rationale:

- PaperMod's document-oriented layout and hard 720px content width made the memorial-site visual design impossible to achieve without rewriting the theme from scratch.
- Astro gives full HTML/CSS control over every page while keeping the same static-output workflow (build → nginx serves files).
- The component model (`Header.astro`, `BaseLayout.astro`, etc.) keeps the design consistent and maintainable as pages are built out.
- Supports future Hebrew/multilingual routing without a full rewrite.
- Build output is plain HTML/CSS/JS in `dist/` — nginx configuration is unchanged except for the `root` directory path.

---

## 6. Content Architecture

Each Markdown file has a YAML front-matter block (title, description, menu weight). The body is standard Markdown.

### Page Map

| Page | URL | Source File | Content from original report |
|------|-----|-------------|-------------------------------|
| Home | `/` | `content/_index.md` | Brief biography, hero portrait |
| Biography | `/biography/` | `content/biography.md` | Sections 1–2 |
| Timeline | `/timeline/` | `content/timeline.md` | Section 4 (all periods) |
| Family Tree | `/family/` | `content/family.md` | Section 5 |
| Documents | `/documents/` | `content/documents.md` | Sections 7–8 |
| History | `/history/` | `content/history.md` | Section 9 |
| Gallery | `/gallery/` | `content/gallery.md` | Personal/ photos with captions |
| Research Notes | `/research/` | `content/research.md` | Sections 3, 6, 10 |

**Status: all content files created** (see `content/` directory).

The original `documents/Herman_Freiman_Research_Report.md` is kept untouched as the canonical reference.

---

## 7. Security & Exposure

### 7.1 Do I need TLS (HTTPS)?

**Short answer: No, not strictly required for this use case — but easy and recommended.**

TLS is essential when passwords, payment data, or personal user information transit the wire. This site has none of that: it is purely read-only static HTML with no login, no forms, and no cookies.

The practical consequences of HTTP-only for this site:
- Modern browsers show a small "Not Secure" label in the address bar (not a blocking warning).
- Some corporate firewalls or aggressive browser settings may warn users before loading HTTP pages.
- Google would rank an HTTPS site higher in search results — irrelevant here since family members will have the direct link.

**Conclusion:** TLS via Let's Encrypt / Certbot takes under 5 minutes, is free, auto-renews, and removes the "Not Secure" label. It is included in the implementation steps as a recommended hardening measure, not a hard requirement. If you choose to skip it, just serve on port 80 only.

### 7.2 Is opening port 80 a security risk?

**Opening port 80 for nginx serving static files is low risk, but the VM itself must be hardened.**

What nginx can and cannot access:
- Nginx runs as the `www-data` system user (not root). It can only read files inside the configured `root` directory (`public/`).
- `/root/`, your SSH keys, GitHub credentials, and every other directory on the VM are **completely inaccessible** to nginx and to anyone browsing the website. The web server root is a narrow, isolated directory containing only pre-built HTML/CSS/image files.
- A static site has no server-side code execution — there is nothing for an attacker to exploit via the web port.

The real risks for the VM are unrelated to the website:
- **SSH brute force** — mitigated by using key-based SSH auth (disable password login).
- **Exposed services** — any other ports should be firewalled.

**Recommended VM hardening (one-time setup, takes 10 minutes):**
```bash
# Allow only SSH, HTTP, and HTTPS; deny everything else
ufw allow 22/tcp
ufw allow 80/tcp
ufw allow 443/tcp     # only if using TLS
ufw enable

# Disable SSH password auth (if not already done)
# In /etc/ssh/sshd_config: PasswordAuthentication no
```

**Defacement risk:** To deface the site an attacker would need SSH access to the VM — which is protected by your SSH key. The web port alone gives no write access to anything.

**Summary:** Open port 80. Harden SSH. Optionally add TLS. Your credentials are safe.

---

## 8. Deployment Architecture

The git repository at `/root/workspace/herman-freiman/` is the Astro project root.

```
/root/workspace/herman-freiman/          ← Git repo + Astro project root
│
├── src/                                 ← Astro source (pages, components, styles)
│   ├── layouts/
│   │   └── BaseLayout.astro            ← HTML shell (fonts, meta, header, footer)
│   ├── components/
│   │   ├── Header.astro                ← Dark header with signature watermark
│   │   └── Footer.astro
│   ├── pages/                          ← One file per URL route
│   │   ├── index.astro                 ← / (home)
│   │   ├── biography.astro             ← /biography/
│   │   ├── timeline.astro
│   │   ├── family.astro
│   │   ├── gallery.astro
│   │   ├── documents.astro
│   │   ├── history.astro
│   │   └── research.astro
│   └── styles/
│       └── global.css                  ← Design tokens, base styles
│
├── public/                             ← Static assets (copied to dist/ as-is)
│   └── photos/                         ← Family photographs
│
├── content/                            ← Hugo Markdown source (pre-migration archive)
│   └── *.md                            ← Will be migrated in Phase 3
│
├── documents/                          ← Original research documents (archived)
│   ├── Herman_Freiman_Research_Report.md
│   ├── Arolsen/
│   ├── Personal/
│   └── Yad_Vashem/
│
├── astro.config.mjs                    ← Astro configuration
├── package.json                        ← npm scripts (build, dev, preview)
└── dist/                               ← Generated output (in .gitignore)
    └── (served by nginx)
```

**Nginx points to:** `/root/workspace/herman-freiman/dist/`

**Update workflow (Phase 1–2 — Astro pages):**
1. Edit any file in `src/pages/` or `src/components/`
2. Run `npm run build` (from `/root/workspace/herman-freiman/`)
3. Nginx immediately serves the regenerated `dist/` directory — no restart needed

**Update workflow (Phase 3+ — after content migration):**
1. Edit the relevant `.astro` page (content will be embedded or imported from Markdown)
2. Run `npm run build`
3. Nginx serves `dist/`

---

## 9. Implementation Steps

### Step 1 — Verify Node.js
Node.js v22 is already present on the VM. Verify:
```bash
node --version   # should print v22.x.x
npm --version    # should print 10.x.x
```
If missing: `apt-get install -y nodejs npm`

### Step 2 — Install Nginx
```bash
apt-get install -y nginx
```

### Step 3 — Install Astro dependencies
```bash
cd /root/workspace/herman-freiman
npm install
```
This reads `package.json` and installs Astro (and any future dependencies) into `node_modules/`. Run this once after cloning, and again after any `package.json` change.

### Step 4 — Astro Configuration
`astro.config.mjs` is in the repo root. The only field normally needed is `site`:
```js
// astro.config.mjs
export default defineConfig({
  site: 'https://hermanfreiman.org',
});
```
Unlike Hugo, Astro does **not** bake the domain URL into navigation links — internal links use root-relative paths (`/biography/`). No rebuild is needed when switching between HTTP and HTTPS.

### Step 5 — Source Files *(already done)*
All Astro source files are in `src/`. Family photographs are in `public/photos/`.

### Step 6 — Local Preview
```bash
cd /root/workspace/herman-freiman
npm run dev
```
Opens a dev server at `http://localhost:8080` (SSH tunnel `-L 8080:localhost:8080` already configured). Hot-reloads on every file save.

### Step 7 — Initial Build
```bash
cd /root/workspace/herman-freiman
npm run build
```
Generates `dist/`. Verify `dist/index.html` exists.

### Step 8 — Configure Nginx
Create `/etc/nginx/sites-available/hermanfreiman.com`:
```nginx
server {
    listen 80;
    server_name hermanfreiman.com www.hermanfreiman.com;

    root /root/workspace/herman-freiman/public;
    index index.html;

    location / {
        try_files $uri $uri/ =404;
    }

    # Cache static assets
    location ~* \.(jpg|jpeg|png|gif|webp|css|js|woff2?)$ {
        expires 30d;
        add_header Cache-Control "public, immutable";
    }

    gzip on;
    gzip_types text/html text/css application/javascript image/svg+xml;
}
```
```bash
ln -s /etc/nginx/sites-available/hermanfreiman.com /etc/nginx/sites-enabled/
nginx -t && systemctl reload nginx
```

### Step 9 — VM Firewall
```bash
ufw allow 22/tcp
ufw allow 80/tcp
ufw enable
```

### Step 10 — DNS
Log in to Cloudflare. Point the `hermanfreiman.org` A records to the Hetzner VM's public IP address (`46.224.66.242`). Propagation typically takes 5–60 minutes.

```
hermanfreiman.org      A    46.224.66.242
www.hermanfreiman.org  A    46.224.66.242
```

Verify propagation:
```bash
dig +short hermanfreiman.org A
# should return: 46.224.66.242
```

### Step 11 — TLS Certificate *(optional but recommended)*
```bash
certbot --nginx -d hermanfreiman.org -d www.hermanfreiman.org --non-interactive --agree-tos -m sfsharon@gmail.com
```
Certbot auto-renews via a systemd timer — no manual action needed.

---

## 10. How to Start the Website

Nginx starts automatically on boot and serves the site at all times. You do not need to do anything to "start" it after the initial setup. To verify it is running:

```bash
systemctl status nginx
```

To restart it if ever needed:

```bash
systemctl restart nginx
```

The site is served from `/root/workspace/herman-freiman/dist/`. Nginx reads that directory directly — no restart is needed after an Astro build.

---

## 11. Updating Content (day-to-day workflow)

> **Important — current state (Phase 1):**
> The `content/` folder contains Markdown files from the original Hugo site. These files are **not connected to the live website** and editing them has no effect on what visitors see. They are an archive held for Phase 3 migration.
>
> The live website is built entirely from `src/pages/*.astro`. To change what appears on a page, edit the relevant file in `src/pages/`.

### Step 1 — Edit the source file

Each page maps to one file:

| Page | File to edit |
|------|-------------|
| Home | `src/pages/index.astro` |
| Biography | `src/pages/biography.astro` |
| Timeline | `src/pages/timeline.astro` |
| Family Tree | `src/pages/family.astro` |
| Gallery | `src/pages/gallery.astro` |
| Documents | `src/pages/documents.astro` |
| History | `src/pages/history.astro` |
| Research Notes | `src/pages/research.astro` |
| Header / navigation | `src/components/Header.astro` |
| Footer | `src/components/Footer.astro` |
| Colors, fonts, spacing | `src/styles/global.css` |

Example:
```bash
nano /root/workspace/herman-freiman/src/pages/biography.astro
```

After Phase 3 (content migration), the biographical text will live in Markdown files that are imported by the `.astro` pages. At that point this table will be updated with the Markdown file paths.

### Step 2 — Preview locally (optional)

```bash
cd /root/workspace/herman-freiman
npm run dev
```

Open `http://localhost:8080` in a browser (via the existing SSH tunnel `-L 8080:localhost:8080`). The dev server hot-reloads on every file save — no manual rebuild needed during editing.

### Step 3 — Build for production

```bash
cd /root/workspace/herman-freiman
npm run build
```

Astro generates `dist/` in under two seconds. Nginx immediately serves the updated files — no nginx restart needed.

### Step 4 — Verify

```bash
curl -sk -H "Host: hermanfreiman.org" https://localhost/biography/ | grep "<title>"
```

Should print: `<title>Biography — Herman (Zvi) Freiman</title>`

---

## 12. Maintenance Cheat Sheet

| Task | Command |
|------|---------|
| Check site is up | `systemctl status nginx` |
| Edit a page | `nano src/pages/<page>.astro` (NOT content/ — see §11) |
| Live-preview while editing | `cd /root/workspace/herman-freiman && npm run dev` → open `http://localhost:8080` (SSH tunnel already on port 8080) |
| Build for production | `cd /root/workspace/herman-freiman && npm run build` |
| Add a photo | Copy to `public/photos/`, reference as `/photos/filename.ext` in any Astro page, rebuild |
| Check nginx is serving | `curl -sk -H "Host: hermanfreiman.org" https://localhost/ \| grep "<title>"` |
| Reload nginx after config change | `nginx -t && systemctl reload nginx` |
| Renew TLS certificate | Automatic (certbot systemd timer) — manual: `certbot renew` |

---

---

## 13. Go-Live Checklist (DNS + TLS)

Run these steps in order once the domain `hermanfreiman.org` is registered on Cloudflare.

### A — Set DNS records (Cloudflare dashboard)
```
hermanfreiman.org      A    46.224.66.242
www.hermanfreiman.org  A    46.224.66.242
```
Wait for propagation (5–60 min), then verify:
```bash
dig +short hermanfreiman.org A
# must return 46.224.66.242 before continuing
```

### B — Build the Astro site
```bash
cd /root/workspace/herman-freiman
npm run build
```
Astro uses root-relative links internally (`/biography/`) so no domain configuration change is needed before or after adding TLS.

Test it:
```bash
curl -s -o /dev/null -w "%{http_code}" http://hermanfreiman.org/
# should return 301 (redirect to HTTPS) or 200
```

### C — Run Certbot (get TLS certificate)
```bash
certbot --nginx -d hermanfreiman.org -d www.hermanfreiman.org \
  --non-interactive --agree-tos -m sfsharon@gmail.com
ufw allow 443/tcp
```
Certbot edits the nginx config automatically to add HTTPS and redirect HTTP → HTTPS.

**Post-certbot fix required.** Certbot replaces the HTTP block with one that returns `404` for any request whose `Host` header is not the domain name. This breaks direct IP access and any relative links clicked while browsing via the IP. After certbot runs, fix the HTTP block in `/etc/nginx/sites-available/hermanfreiman.org`:

Change:
```nginx
    listen 80;
    listen [::]:80;
    server_name hermanfreiman.org www.hermanfreiman.org;
    return 404; # managed by Certbot
```
To:
```nginx
    listen 80 default_server;
    listen [::]:80 default_server;
    server_name hermanfreiman.org www.hermanfreiman.org _;
    return 301 https://hermanfreiman.org$request_uri;
```
Then reload nginx:
```bash
nginx -t && systemctl reload nginx
```
This ensures any direct IP request redirects cleanly to the domain instead of 404-ing.

### D — Verify HTTPS
Open `https://hermanfreiman.org` in your phone browser. Check the padlock icon is shown. Navigate through all menu tabs to confirm everything works.

---

*Document status: **Live at https://hermanfreiman.org — Astro v4, nginx, Cloudflare DNS, Let's Encrypt TLS (auto-renews). Phase 1 scaffold complete; Phase 2 (visual homepage) in progress.***
*Last updated: June 2026*
