# Completion Status — garnett-enterprises

> Refined status doc. Verified against source files on `main` 2026-05-19.

**Score:** 50 / 100 — Working static brochure, but **orphaned**: the live domain serves a different (Squarespace) site
**Live URL inspection:** `curl -sI https://www.garnettenterprises.com` returns:
- `HTTP/2 200`
- `server: cloudflare` (Cloudflare proxy)
- `set-cookie: crumb=…` (Squarespace anti-CSRF cookie)
- `x-contextid: …` (Squarespace edge)

`curl -s https://www.garnettenterprises.com | grep -i squarespace` returns `<!-- This is Squarespace. --><!-- flute-oarfish-zxsc -->` and OG/favicon URLs on `images.squarespace-cdn.com` + `static1.squarespace.com`. Squarespace site uses **Roboto** (per `<link rel="stylesheet" href="…fonts.googleapis.com/css2?family=Roboto…">`) — this repo uses Playfair Display + Source Sans 3. Even the typography diverges from the live brand.

DNS: `dig +short garnettenterprises.com` returns `172.67.147.192`, `104.21.28.226` — both Cloudflare anycast. Cloudflare is fronting Squarespace.

**Last touched:** Repo single commit dated **2026-01-30** (`pushed_at` 2026-05-19 reflects housekeeping). 109 days since real activity.

## Stack (verified)

| Layer | Tech |
|---|---|
| Markup | Plain HTML5 — 2 pages (`index.html` 172 lines, `about.html` 172 lines) |
| Styles | Hand-written CSS3 — `css/styles.css`, 531 lines, uses CSS custom properties, Grid, Flexbox |
| JS | None (no build step). Two small inline `<script>` blocks for header scroll effect + smooth scroll |
| Fonts | Google Fonts: Playfair Display (display) + Source Sans 3 (body) |
| Logo | Inline SVG (hexagon outline + serif "G"), repeated as `data:` favicon |
| Forms | Netlify Forms — `data-netlify="true"`, `netlify-honeypot="bot-field"`, hidden `form-name` input |
| Hosting target | Netlify (per `netlify.toml`) with SPA fallback redirect `/* → /index.html 200` |
| Headers | `netlify.toml` sets `X-Frame-Options: DENY`, `X-XSS-Protection`, `X-Content-Type-Options: nosniff`, `Referrer-Policy: strict-origin-when-cross-origin`, and `Cache-Control: public, max-age=31536000, immutable` for `/css/*` and `/assets/*` |

## File inventory (verified — every file in repo)

| Path | Lines | Purpose |
|---|---:|---|
| `.gitignore` | 14 | Standard Node ignores |
| `COMPLETION_STATUS.md` | 31 | Prior status doc (the one being refined here) |
| `README.md` | 99 | Setup + customization notes; references nonexistent `assets/` folder |
| `index.html` | 172 | Hero + Intro + Philosophy + Contact (Netlify form `name="contact"`) |
| `about.html` | 172 | Page header + Team (2 members, Unsplash placeholders w/ `onerror` fallback) + Experience + Contact (Netlify form `name="contact-about"`, plus newsletter checkbox) |
| `css/styles.css` | 531 | Single stylesheet, CSS variables, responsive |
| `netlify.toml` | 27 | Build (`publish="."`), SPA redirect, security headers, long-cache for `/css/*` + `/assets/*` |

**Total: 7 tracked files, ~1,046 lines of code/markup.**

## What works
- Two-page brochure: home + about
- Semantic markup, responsive viewport, SEO `<meta name="description">` per page
- Netlify Forms wired with honeypot (`bot-field`) on both pages — two distinct form names (`contact`, `contact-about`)
- Real contact info embedded:
  - Brynr Garnett — brynr@garnettenterprises.com, (806) 270-0338, Spring TX
  - James Rydjeski, CPA — james@garnettenterprises.com, (949) 510-2905, Clinton MD
- Inline SVG logo (no extra HTTP request)
- Header scroll-state class + smooth anchor scrolling via inline JS
- Sensible security headers via `netlify.toml`

## Known gaps (verified)
- **Live brand divergence (P0):** The repo is **not** serving `garnettenterprises.com`. Squarespace is. Any edits here are invisible to customers.
- **No `assets/` folder committed.** `about.html` lines 48–63 use `images.unsplash.com` URLs as placeholder team photos, with `onerror` falling back to `placehold.co`. README's "Customization → Adding Team Photos" instructions reference an `assets/` folder that doesn't exist in the repo.
- **`index.html` hero image is an Unsplash CDN URL** (`photo-1506905925346-21bda4d32df4`). LCP will be hostage to a 3rd-party CDN.
- **No `robots.txt`, `sitemap.xml`, OG/Twitter meta tags** beyond the basic description.
- **No favicon file** — only the inline `data:image/svg+xml,…` favicon. Some platforms (older browsers, link previewers) want a real `.ico`.
- **No analytics** — no GA4, no Plausible, nothing.
- **No accessibility audit** — labels and structure look OK, but no formal Lighthouse run. The `data-netlify="true"` attribute is in a `<form>` that also uses native `required` — Netlify recommends keeping the hidden `form-name` field which IS present.
- **Newsletter checkbox is only on `about.html` form**, not the home contact form. Inconsistency.
- **No license file.** README footer says "© 2025" but no `LICENSE`.
- **SPA fallback `/* → /index.html 200` is wrong for a 2-page site** — it would cause `/about` to serve home content if anyone ever links without `.html`. Should be removed or limited.
- The `about.html` script tag is missing the smooth-scroll block that `index.html` has. Minor inconsistency.

## Strategic context
This repo is one of two candidate canonical surfaces for the Garnett Enterprises brand. The other is a Squarespace site. They have diverged on:
- Typography (Roboto vs Playfair/Source Sans 3)
- Image sources (Squarespace CDN vs Unsplash)
- Edit cadence (Squarespace is presumably current; repo is 3+ months stale)

**Decision needed from owner before any code work.** See `IMPROVEMENT_PLAN.md` P0.

## Notes for AI agents
- **Do NOT edit this repo's HTML/CSS until the owner picks a canonical surface.** Any changes are wasted work if Squarespace is the keeper.
- **Do NOT autonomously swap DNS** from Squarespace to Netlify. That's a destructive, billing-affecting action requiring explicit human approval.
- If activating this repo:
  - Cancel Squarespace plan first (avoid double-billing)
  - Move DNS for `garnettenterprises.com` from Cloudflare-fronted-Squarespace to Netlify
  - Re-validate Netlify Forms (form name must match `index.html`'s `form-name` value or submissions silently drop)
  - Commit real photos to `assets/` and stop relying on Unsplash
- If archiving this repo: just `gh repo archive Brynrg/garnett-enterprises` after the owner confirms.
