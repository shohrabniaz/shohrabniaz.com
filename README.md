# shohrabniaz.com

Combined **academic** and **professional** personal website for **Shohrab Hossen Niaz** (Niaz) —
MSc Cyber Security & Forensics researcher (University of the Sunshine Coast) and Senior DevOps,
Platform & Cloud Security Engineer based in Adelaide, South Australia.

Built with [Jekyll](https://jekyllrb.com/) and deployed to **GitHub Pages** via GitHub Actions.

## Live preview

While the custom domain is not yet registered, the site is configured to build and render as a
GitHub Pages **project site**:

> **https://shohrabniaz.github.io/shohrabniaz.com/**

There is intentionally **no `CNAME` file** committed yet — adding one before `shohrabniaz.com`
is registered would break this preview URL.

## Project structure

```
.
├── _config.yml            # Site config (url / baseurl, SEO, nav)
├── _data/                 # Structured content (ground truth from the CVs)
│   ├── experience.yml     #   professional roles
│   ├── skills.yml         #   technical stack
│   ├── certifications.yml
│   ├── projects.yml
│   ├── research.yml       #   interests, works-in-progress, education
│   ├── honours.yml
│   └── news.yml           #   highlights timeline
├── _includes/             # head (SEO/OG), nav, footer
├── _layouts/              # default, page
├── assets/
│   ├── css/main.css       # design system + light/dark themes
│   ├── js/theme.js        # theme toggle (localStorage + system preference)
│   ├── img/               # favicon + Open Graph image
│   └── pdf/               # Academic CV + Industry CV (downloadable)
├── index.html             # About / Home
├── research.html          # Research
├── experience.html        # Experience
├── projects.html          # Projects
├── cv.html                # CV (web summary + PDF downloads)
├── contact.html           # Contact
└── .github/workflows/pages.yml   # build + deploy to GitHub Pages
```

## Local development

Requires Ruby (3.x) and Bundler.

```bash
bundle install
bundle exec jekyll serve
```

Then open **http://127.0.0.1:4000/shohrabniaz.com/**.

To produce the static output in `_site/` without serving:

```bash
bundle exec jekyll build
```

## Deployment

Every push to `main` triggers `.github/workflows/pages.yml`, which builds the site with Jekyll and
publishes it to GitHub Pages. In the repository settings, set **Settings → Pages → Build and
deployment → Source** to **GitHub Actions** (one-time setup).

The workflow passes GitHub Pages' resolved base path to Jekyll at build time, so the deployed URL
stays correct whether the site is served from `/shohrabniaz.com/` (project site) or `/` (custom domain).

## Features

- Dual academic + professional positioning, with full coverage of both profiles.
- Light / dark theme toggle, persisted in `localStorage` and respecting the system preference
  (set before first paint, so there is no flash of the wrong theme).
- Responsive layout (desktop + mobile) with a clean type system.
- SEO meta tags, Open Graph / Twitter card metadata, `sitemap.xml`, `robots.txt` and an Atom feed.
- All content is driven from `_data/*.yml` and sourced **only** from Niaz's two CVs — no fabricated
  papers, metrics, dates or certifications. In-progress research is honestly labelled as such.

---

## Switching to the custom domain (`www.shohrabniaz.com`)

Do this **after** registering `shohrabniaz.com` on **Cloudflare Registrar**. It is a two-step change.

### Step 1 — Tell the site about the new domain

1. **Add a `CNAME` file** at the repository root containing exactly:

   ```
   www.shohrabniaz.com
   ```

2. **Update `_config.yml`** — change the two preview values to the custom domain:

   ```yaml
   url: "https://www.shohrabniaz.com"
   baseurl: ""
   ```

   (Currently these are `https://shohrabniaz.github.io` and `/shohrabniaz.com` for the preview.)

3. Commit and push to `main`. The Pages workflow redeploys automatically. In **Settings → Pages →
   Custom domain**, enter `www.shohrabniaz.com` and enable **Enforce HTTPS** once the certificate
   is issued.

### Step 2 — Point Cloudflare DNS at GitHub Pages

In the Cloudflare dashboard for `shohrabniaz.com`, under **DNS → Records**, add:

| Type  | Name   | Content / Target               | Proxy status   |
| ----- | ------ | ------------------------------ | -------------- |
| CNAME | `www`  | `shohrabniaz.github.io`        | DNS only (grey)|

Then make the apex (`shohrabniaz.com`) redirect to `www` so both work:

- Add four **A records** on the apex (`@`) pointing at GitHub Pages' IPs, **DNS only**:
  `185.199.108.153`, `185.199.109.153`, `185.199.110.153`, `185.199.111.153`
  (and optionally the matching `AAAA` records:
  `2606:50c0:8000::153`, `2606:50c0:8001::153`, `2606:50c0:8002::153`, `2606:50c0:8003::153`).
- **Or** create a Cloudflare **Redirect Rule / Bulk Redirect** from `shohrabniaz.com/*` to
  `https://www.shohrabniaz.com/$1` (301).

> **Tips**
> - Keep the `www` CNAME (and the apex A/AAAA records) set to **DNS only** (grey cloud) at least
>   until GitHub has issued the TLS certificate; Cloudflare proxying can interfere with GitHub's
>   automatic certificate provisioning. You can enable the orange-cloud proxy afterwards if desired.
> - DNS and certificate propagation can take a little time; if the custom domain shows a warning in
>   GitHub Pages settings, wait and re-check.

To revert to the preview URL, delete the `CNAME` file and restore the preview `url` / `baseurl`
values in `_config.yml`.
