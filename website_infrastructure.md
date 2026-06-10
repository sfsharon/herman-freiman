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
  - [Option A — Hugo SELECTED](#option-a--hugo-selected)
  - [Option B — Eleventy (11ty)](#option-b--eleventy-11ty-not-selected)
  - [Option C — Astro](#option-c--astro-not-selected)
  - [Option D — Plain HTML + Pandoc](#option-d--plain-html--pandoc-not-selected)
- [4. Comparison Matrix](#4-comparison-matrix)
- [5. Selected Option: Hugo + PaperMod](#5-selected-option-hugo--papermod)
- [6. Content Architecture](#6-content-architecture)
  - [Page Map](#page-map)
- [7. Security & Exposure](#7-security--exposure)
  - [7.1 Do I need TLS (HTTPS)?](#71-do-i-need-tls-https)
  - [7.2 Is opening port 80 a security risk?](#72-is-opening-port-80-a-security-risk)
- [8. Deployment Architecture](#8-deployment-architecture)
- [9. Implementation Steps](#9-implementation-steps)
  - [Step 1 — Install Hugo](#step-1--install-hugo-extended-version)
  - [Step 2 — Install Nginx](#step-2--install-nginx)
  - [Step 3 — Add PaperMod as a Git Submodule](#step-3--add-papermod-as-a-git-submodule)
  - [Step 4 — Hugo Configuration](#step-4--hugo-configuration)
  - [Step 5 — Content Files](#step-5--content-files-already-done)
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
  - [B — Rebuild Hugo with the real domain](#b--rebuild-hugo-with-the-real-domain-http)
  - [C — Run Certbot](#c--run-certbot-get-tls-certificate)
  - [D — Switch Hugo to HTTPS and rebuild](#d--switch-hugo-to-https-and-rebuild)
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

### Option A — Hugo SELECTED

**What it is:** A static site generator written in Go. Converts Markdown → HTML at build time. Distributed as a single binary.

| | |
|-|-|
| **Language** | Go (single self-contained binary, no runtime to manage) |
| **Build time** | < 1 second for a site this size |
| **Markdown support** | Native; front-matter (YAML/TOML) per file |
| **Theme** | PaperMod — minimal, clean, fully responsive |
| **Serving** | Build output is plain HTML/CSS/JS → served by nginx |
| **Maintenance** | Edit `.md` file → run `hugo` → nginx serves updated files |

**Pros**
- No Node.js/Python needed at build or serve time — single binary
- Fastest build of any option
- PaperMod theme is dignified, uncluttered, and mobile-first out of the box
- Native image processing handles the photo gallery
- Excellent multilingual support (useful if Hebrew sections grow)

**Cons**
- Go templating syntax (`{{ .Title }}`) takes some getting used to
- Less flexible if interactive components are ever needed

---

### Option B — Eleventy (11ty) *(not selected)*

Node.js SSG, highly flexible, mostly DIY CSS/layout. ~4–6 hours of setup effort due to no ready-made theme that suits this content.

### Option C — Astro *(not selected)*

Modern Node.js, content-first, zero JS shipped to browser. Good option but newer/more complex than needed for a temporary family site.

### Option D — Plain HTML + Pandoc *(not selected)*

Absolute minimal dependencies but requires all CSS/layout work by hand. ~5–8 hours effort.

---

## 4. Comparison Matrix

| Criterion | **Hugo** | Eleventy | Astro | Pandoc+HTML |
|-----------|----------|----------|-------|-------------|
| Setup speed | **Fast** | Medium | Medium | Fast |
| Markdown → pages | **Automatic** | Automatic | Automatic | Manual script |
| Polished theme available | **Yes** | Partial | Yes | No |
| Node.js dependency | **No** | Yes | Yes | No |
| Responsive out of box | **Yes (PaperMod)** | Manual | Theme-dep. | Manual |
| Photo gallery | **Built-in shortcode** | Manual | Manual | Manual |
| Admin editing | **Edit MD + `hugo`** | Edit MD + npx | Edit MD + npm | Edit MD + script |
| Long-term maintainability | **High** | High | Medium-High | Medium |

---

## 5. Selected Option: Hugo + PaperMod

**Hugo** with the **PaperMod** theme was selected. Rationale:

- PaperMod renders cleanly on mobile and large screens (requirements 1 & 2) with a dignified, minimal aesthetic appropriate for a memorial site.
- The fastest path from "edit a Markdown file" to "website updated" (requirement 5).
- Single binary — no runtime process running on the VM; nginx serves pre-built static files only.
- Native image processing and `figure` shortcodes handle the family photo gallery.

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

The existing git repository at `/root/workspace/herman-freiman/` **is** the Hugo project root. No second repository or `/opt/` location is needed.

```
/root/workspace/herman-freiman/          ← Git repo + Hugo project root
│
├── content/                             ← Markdown source (single truth point)
│   ├── _index.md
│   ├── biography.md
│   ├── timeline.md
│   ├── family.md
│   ├── documents.md
│   ├── history.md
│   ├── gallery.md
│   └── research.md
│
├── static/photos/                       ← Family photographs (served as-is)
├── themes/PaperMod/                     ← Git submodule (read-only theme)
├── documents/                           ← Original source documents (archived)
│   ├── Herman_Freiman_Research_Report.md
│   ├── Arolsen/
│   ├── Personal/
│   └── Yad_Vashem/
├── hugo.toml                            ← Site configuration
└── public/                             ← Generated output (in .gitignore)
    └── (served by nginx)
```

**Nginx points to:** `/root/workspace/herman-freiman/public/`

**Update workflow:**
1. Edit any `content/*.md` file
2. Run `hugo --minify` (from `/root/workspace/herman-freiman/`)
3. Nginx immediately serves the regenerated `public/` directory — no restart needed

---

## 9. Implementation Steps

### Step 1 — Install Hugo (extended version)
```bash
# Check if apt version is recent enough (need v0.112+)
apt-get install -y hugo
hugo version

# If the apt version is older than v0.112, download the latest binary instead:
# wget https://github.com/gohugoio/hugo/releases/latest/download/hugo_extended_X.Y.Z_linux-amd64.deb
# dpkg -i hugo_extended_*.deb
```

### Step 2 — Install Nginx
```bash
apt-get install -y nginx
```

### Step 3 — Add PaperMod as a Git Submodule
```bash
# Run from the existing repo root — no git init needed
cd /root/workspace/herman-freiman
git submodule add --depth=1 https://github.com/adityatelange/hugo-PaperMod themes/PaperMod
git submodule update --init --recursive
```

### Step 4 — Hugo Configuration
`hugo.toml` is already created in the repo root. Review and adjust `baseURL` to match the final domain.

### Step 5 — Content Files *(already done)*
All eight `content/*.md` files are created and populated. `static/photos/` contains all family photographs.

### Step 6 — Local Preview
```bash
cd /root/workspace/herman-freiman
hugo server        # live preview at http://localhost:1313
```
Review all pages on desktop and mobile (browser dev tools). Check Hebrew text rendering.

### Step 7 — Initial Build
```bash
cd /root/workspace/herman-freiman
hugo --minify
```
This generates `public/`. Verify `public/index.html` exists.

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

The site is served from `/root/workspace/herman-freiman/public/`. Nginx reads that directory directly — no restart is needed after a Hugo rebuild.

---

## 11. Updating Content (day-to-day workflow)

### Step 1 — Edit the Markdown file
Open any file in `content/` and save your changes. Example:
```bash
nano /root/workspace/herman-freiman/content/biography.md
```

### Step 2 — Rebuild the site
Run Hugo from the project directory. **Which command depends on DNS status:**

| DNS situation | Build command |
|---------------|--------------|
| DNS not set up yet — testing via IP `46.224.66.242` | `hugo --minify -b http://46.224.66.242/` |
| DNS live, HTTP only (`hermanfreiman.org` resolves) | `hugo --minify` (baseURL in hugo.toml is `http://hermanfreiman.org/`) |
| DNS live + TLS active (after certbot) | Change `baseURL` in `hugo.toml` to `https://hermanfreiman.org/`, then `hugo --minify` |

**Important:** After certbot runs, the `https://` step is mandatory. Hugo bakes the baseURL into every nav link. If the links say `http://` while the site is served over `https://` (via Cloudflare), clicking any tab will cause a redirect loop or mixed-content failure.

Hugo regenerates `public/` in under a second. Nginx immediately serves the updated files — no nginx restart needed.

### Why the different build commands?
Hugo bakes the `baseURL` into every navigation link in the generated HTML. If the URL in `hugo.toml` is `http://hermanfreiman.com/` but DNS is not yet pointing here, every menu click will try to reach a domain that doesn't exist and fail. Passing `-b http://46.224.66.242/` overrides that for the current build only without changing `hugo.toml`.

### Step 3 — Verify
```bash
curl -s -o /dev/null -w "%{http_code}" http://localhost/biography/
# should print: 200
```

---

## 12. Maintenance Cheat Sheet

| Task | Command |
|------|---------|
| Check site is up | `systemctl status nginx` |
| Edit a page | `nano content/<page>.md` |
| Rebuild (DNS not ready) | `cd /root/workspace/herman-freiman && hugo --minify -b http://46.224.66.242/` |
| Rebuild (DNS ready, HTTP) | `cd /root/workspace/herman-freiman && hugo --minify` |
| Live-preview while editing | `cd /root/workspace/herman-freiman && hugo server` → open `http://localhost:1313` (requires `-L 1313:localhost:1313` in SSH command) |
| Add a photo | Copy to `static/photos/`, add `{{</* figure */>}}` shortcode in `gallery.md`, rebuild |
| Switch to HTTPS after certbot | Edit `baseURL` in `hugo.toml` → `https://hermanfreiman.org/`, then `hugo --minify` |
| TLS renewal | Automatic — certbot systemd timer handles it, no action needed |

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

### B — Rebuild Hugo with the real domain (HTTP)
```bash
cd /root/workspace/herman-freiman
hugo --minify
```
`hugo.toml` already has `baseURL = "http://hermanfreiman.org/"` — no edit needed.

Test it:
```bash
curl -s -o /dev/null -w "%{http_code}" http://hermanfreiman.org/
# should return 200
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

### D — Switch Hugo to HTTPS and rebuild
Edit `hugo.toml`:
```toml
baseURL = "https://hermanfreiman.org/"
```
Then rebuild:
```bash
hugo --minify
```

### E — Verify
Open `https://hermanfreiman.org` in your phone browser. Check the padlock icon is shown. Navigate through all menu tabs to confirm everything works.

---

*Document status: **Deployed via IP. Domain: hermanfreiman.org (pending registration). DNS and TLS pending.***
*Last updated: June 2026*
