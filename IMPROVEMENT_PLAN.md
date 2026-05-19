# Improvement Plan — garnett-enterprises

Prioritized P0 → P3. P0 is a **strategic decision**, not a code change.

---

## P0 — STRATEGIC DECISION (owner-only)

### P0-1. Resolve the Squarespace-vs-Netlify divergence

**The problem (verified):**

- `https://www.garnettenterprises.com` is serving a **Squarespace** site, fronted by Cloudflare.
  - HTML body contains `<!-- This is Squarespace. -->`
  - Response headers include `set-cookie: crumb=…`, `x-contextid: …`, Cloudflare `server: cloudflare`
  - Assets load from `images.squarespace-cdn.com` and `static1.squarespace.com`
  - Site uses **Roboto** font
- This **`garnett-enterprises` repo** is a hand-coded HTML/CSS site, designed to deploy to **Netlify**, that has never been routed to the production domain.
  - Uses Playfair Display + Source Sans 3
  - Last meaningful commit 2026-01-30 — 3.5 months stale
  - References an `assets/` folder that doesn't exist; uses Unsplash placeholders for team photos
  - Single commit `Initial commit` — never iterated post-launch

**The two surfaces have diverged on typography, imagery, and presumably content edits made on Squarespace since January.** This repo is orphaned code.

**Action: do NOT autonomously make this choice. Present the owner with these options.**

### Option A — Cancel Squarespace, activate this repo on Netlify

**Cost/effort estimate:** ~4-6 hours of focused work + content reconciliation
**What this looks like:**

1. **Content reconciliation (owner does first):**
   - Open the live Squarespace site and diff content against `index.html`/`about.html` in this repo.
   - Pull any newer copy back into this repo (commit it).
   - Export any Squarespace form submissions (if Squarespace Forms is in use).
2. **Add real `assets/`:**
   - Replace the `images.unsplash.com` hero with a licensed/owned image, committed at `assets/hero.jpg` (~1200px wide, WebP+JPG fallback).
   - Replace the two Unsplash team photo URLs with real headshots at `assets/team/brynr.jpg` and `assets/team/james.jpg` (~600×800).
3. **Pre-cutover deploy to Netlify** on a staging URL (`garnett-enterprises.netlify.app`). Verify:
   - Both Netlify Forms (`contact`, `contact-about`) capture submissions in Netlify dashboard
   - Lighthouse mobile ≥85 perf, ≥95 a11y, ≥95 best-practices, ≥95 SEO
   - All links work (header logo to `index.html`, `about.html` nav, `#contact` anchor)
4. **DNS cutover (destructive — needs explicit user approval):**
   - Cancel Squarespace plan (avoid double-billing) — owner does in Squarespace billing
   - In Cloudflare DNS, change `garnettenterprises.com` and `www.garnettenterprises.com` from Squarespace targets to Netlify (`<site>.netlify.app` or Netlify-supplied A/AAAA records)
   - Keep Cloudflare proxy ON, set SSL mode to **Full (strict)**
   - Add the apex + www domains in Netlify "Domain management"
   - Verify HTTPS certificate provisions (Netlify handles via Let's Encrypt)
5. **Post-cutover:**
   - Submit `https://www.garnettenterprises.com/sitemap.xml` (P1 item — create this) to Google Search Console
   - Test both contact forms end-to-end (submit a real test to each)
   - Verify Cloudflare cache rules don't conflict with Netlify form posts (POST should bypass cache automatically)
   - Set up Netlify Forms notifications → forward to brynr@garnettenterprises.com

**Risk:** ~30-90 min DNS propagation window where the site may be unreachable or serve stale content. Recommend scheduling for low-traffic time.

### Option B — Archive this repo

**Cost/effort estimate:** 5 minutes
**What this looks like:**

1. Owner confirms Squarespace is the keeper.
2. Run `gh repo archive Brynrg/garnett-enterprises`.
3. Optionally update the repo's README to say "Archived — production site is Squarespace at https://www.garnettenterprises.com."
4. Delete the repo from any active project trackers.

**Why this is reasonable:**
- A 2-page brochure site doesn't benefit much from a static-HTML repo over Squarespace if Brynr isn't actively coding it
- Squarespace handles forms, SSL, email forwarding, image CDN, mobile responsiveness, and CMS edits — features this repo would need to rebuild
- Zero stars, zero issues, zero PRs, one commit — the repo has no community or history to preserve

### Decision criteria — for the owner

Answer yes/no to these:
1. Do I want pixel-control over the site's design?
2. Am I going to edit copy more than 1× per quarter via git?
3. Do I want the site under version control for some legal/IP reason?
4. Am I willing to pay $0 (Netlify free tier) and lose $16-25/mo (Squarespace)?

If 3+ yeses → **Option A**. If 2 or fewer → **Option B**.

**Do not proceed with any P1+ items below until P0 is resolved.** P1+ assumes Option A was chosen.

---

## P1 — If Option A chosen, ship before cutover

- **Add real assets:** commit `assets/hero.{jpg,webp}` and `assets/team/{brynr,james}.{jpg,webp}` — see Option A step 2.
- **Replace `<img src="https://images.unsplash.com/…">`** in `index.html` line 40 and `about.html` lines 48, 60 with local paths. Add `loading="eager"` + `fetchpriority="high"` for the hero; `loading="lazy"` for team photos.
- **Add `<picture>`** with WebP source + JPG fallback for hero (LCP win).
- **Add OG + Twitter meta tags** in both pages:
  ```html
  <meta property="og:title" content="Garnett Enterprises | Elevating Businesses, Maximizing Potential">
  <meta property="og:description" content="…">
  <meta property="og:image" content="https://www.garnettenterprises.com/assets/og.jpg">
  <meta property="og:type" content="website">
  <meta property="og:url" content="https://www.garnettenterprises.com/">
  <meta name="twitter:card" content="summary_large_image">
  ```
- **Add `robots.txt`** at repo root:
  ```
  User-agent: *
  Allow: /
  Sitemap: https://www.garnettenterprises.com/sitemap.xml
  ```
- **Add `sitemap.xml`** at repo root listing `/`, `/about.html`.
- **Add a real `favicon.ico`** (16, 32, 48 multi-size) at `/favicon.ico` and an `apple-touch-icon.png` at `/apple-touch-icon.png`. Keep the SVG `data:` favicon as a fallback `<link rel="alternate icon">`.
- **Fix the SPA fallback** in `netlify.toml` lines 4–8. The current `from = "/*" to = "/index.html" status = 200` is wrong for a static 2-page site — it will serve home content under any path that doesn't match an existing file. Either delete the redirect or limit scope (e.g. `force = false`, paths-list-based).
- **Reconcile content** with whatever copy is currently on Squarespace (owner does this in step 1 of Option A).
- **Newsletter checkbox parity:** if the about-page form has a `newsletter` checkbox (line 138 of `about.html`), add the same checkbox to the home-page contact form for consistency, OR remove it from `about.html`.
- **License:** add `LICENSE` file (proprietary/all-rights-reserved is fine; just be explicit).

## P2 — Polish (post-cutover)

- **Lighthouse CI** in GitHub Actions — fail build if mobile perf < 90, a11y < 95.
- **Analytics:** add Plausible (privacy-friendly) or GA4. Plausible is simpler for a brochure site and avoids cookie-consent banner complexity.
- **`prefers-reduced-motion`** handling for the smooth scroll script.
- **Self-host Google Fonts** to drop the `fonts.googleapis.com` preconnect + the FOIT — `npx google-fonts-helper` or copy to `/css/fonts/`. Removes one third-party request.
- **Form spam hardening:** Netlify Honeypot is in place; consider adding Netlify reCAPTCHA v3 if real spam volume becomes an issue. (Don't add preemptively — adds friction.)
- **404 page** — add `404.html` (Netlify auto-serves it).
- **Reuse contact-section markup** between the two pages — currently duplicated nearly verbatim. Extract to a partial via a simple build step (or accept duplication for a 2-page site).
- **Schema.org JSON-LD:** add `Organization` schema with `legalName`, `email`, `telephone`, `address`, `founder` array (Brynr, James). Helps the GBP listing reconcile with the site.

## P3 — Stretch (months out)

- **Acquisition deal flow form:** dedicated form for sellers with structured fields (industry, revenue range, EBITDA, geography). Better than a free-form "Message" textarea for qualifying inbound.
- **Case-study / portfolio page** as Garnett Enterprises accumulates closed acquisitions — proof of execution helps inbound from sellers.
- **Blog / "Operator Notes"** as a content marketing surface — but only if a real writing cadence is committed. Otherwise this is dead weight.
- **Migrate to a tiny framework (Astro)** if the site grows past 5+ pages, for shared layouts and content collections. Don't bother for 2 pages.
- **Email forwarding via Cloudflare Email Routing** (free) — already at Cloudflare anyway. Forward `brynr@garnettenterprises.com` and `james@garnettenterprises.com` to personal inboxes without paying Google Workspace.
