# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What This Is

Static marketing site for [Tradi2o](https://tradi2o.in), deployed to **Cloudflare Pages**. No build step — all files are served as-is.

- `index.html` — main landing page (self-contained: all CSS/JS inlined)
- `brochure.html` — product brochure page
- `images/` — static assets (SVG logos, JPEG backgrounds, PNG screenshots)
- `updates/<component>/update.json` — Tauri auto-updater manifests (one per component)
- `_headers` — Cloudflare Pages custom response headers (CORS for `/updates/*`, cache control)
- `_redirects` — Cloudflare Pages URL redirects (`/brochure → /`)

## Deployment

Push to `main` triggers a Cloudflare Pages deploy automatically. No CI pipeline in this repo.

To preview locally, any static file server works:

```bash
python3 -m http.server 8080
# or
npx serve .
```

## Update Manifests (`updates/`)

Each `update.json` is fetched by the Tauri updater in Vista (desktop app) and other components. The `_headers` file sets `Access-Control-Allow-Origin: *` on all `/updates/*` responses so Tauri can fetch cross-origin.

When cutting a new release, update the relevant `update.json` files:
- `version` — semver string (e.g. `"2.4.0"`)
- `pub_date` / `published_at` — ISO 8601 UTC timestamp
- `build` — integer, format `YYYYMMDDnn` (e.g. `2026042801`)
- `platforms` — update the `url` and `signature` fields for each platform
- `min_supported_build` — set to new build number if this release breaks backward compat

## Styling Conventions

`index.html` uses CSS custom properties with two themes (`[data-theme="light"]` overrides `:root` dark defaults). Colors are expressed in `oklch()`. Theme toggle is handled by inline JS at the bottom of the file. Do not extract styles to external files — the single-file approach is intentional for zero-dependency delivery.

Fonts: Inter (body) and JetBrains Mono (code/data), loaded from Google Fonts.
