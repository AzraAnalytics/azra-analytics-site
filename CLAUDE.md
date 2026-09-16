# Azra Analytics — Website Project

## What this is
The Azra Analytics website: a **plain HTML + CSS static site** — no build tools, no Node
required — that the owner fully controls in code. Clean, professional, **mobile-first**.

**This is the live site.** It is deployed on **Netlify** (site `remarkable-eclair-3f9c15`) and
serves `azraanalytics.com` and `www.azraanalytics.com` over HTTPS. It replaced the old Wix
site; Wix is no longer involved. **Netlify auto-deploys every push to `main`** on
`github.com/AzraAnalytics/azra-analytics-site` — there is no build step and no manual deploy
action, so pushing *is* publishing. Treat `main` as production. See `deploy.md` for the full
live wiring (DNS at Squarespace, Google Workspace email) and how to check a deploy.

Company: Azra Analytics — a strategy-driven data & AI consulting firm.
Tagline: "Strategy Driven AI and Data" / "Turn your data into intelligent workflows and better decisions."

## Why we rebuilt (history — the Wix site is gone, this replaced it)
- The original site was on Wix (classic Wix Editor). Wix does **not** allow exporting site code.
- Owner wanted Claude to directly edit styling and make it mobile-friendly, which isn't possible
  inside Wix. So we started a fresh code-based site from the existing content instead.
- Owner is non-technical; keep things simple. No Node/npm on the original machine (Git + Python are installed).
- The live Wix site is actually **multi-page**, not a single scroller (discovered by checking real
  nav hrefs) — this rebuild now mirrors that: `/team`, `/customers`, `/services-and-capabilities`,
  `/contact`, `/ai-adoption-tracker` on Wix map to the pages below.

## Tech / structure
- `index.html` — Home: header, hero, About/mission blurb, Services teaser (4 cards + link),
  Projects teaser (3 cards + link), Team teaser (photos/titles + link), Contact CTA, footer.
- `team.html` — full team bios (all 3 members).
- `projects.html` — all 4 projects with full descriptions (includes the Pakistan public-health
  project, which only lives on this page, not the homepage teaser).
- `services.html` — numbered 01–04 service breakdown with deliverables, matching the live
  `/services-and-capabilities` page structure.
- `contact.html` — the real contact form (First/Last name, Email, Phone, Position, Company,
  message). **No backend** — on submit, JS builds a `mailto:omar@azraanalytics.com` link from the
  field values and opens the visitor's email client. That's the honest option for a static site
  with no server; if real form submissions (without opening an email client) are wanted later,
  it'd need a form service like Formspree or a Netlify Forms attribute.
- `ai-adoption-tracker.html` — landing page replicating the live promo page; the actual interactive
  tracker is a separate app the live site links out to (`https://ai-adoption-tracker.onrender.com/`),
  not something built into this static site.
- `faq.html` — 8 retrieval-oriented Q&A on how engagements are scoped, ML vs. rules, automate vs.
  augment, auditability, etc. Doubles as grounding for a future on-site assistant. Styled with
  `.faq-item` in `styles.css`.
- `readiness.html` — the AI/Data Readiness Assessment (Phase 1 flagship lead magnet). 15 questions,
  3 per dimension, each scored 0–3; five dimensions (data foundations, governance & trust,
  capability & ownership, use-case pipeline, decision culture) → 0–100 each, overall = sum/45.
  Bands: Exploring 0–34, Building 35–54, Operating 55–74, Compounding 75–100. Results = score ring,
  hand-drawn SVG radar, per-dimension read, and **three next moves** taken from the three
  lowest-scoring dimensions (each dimension has one move per band, so the output is fully
  deterministic; ties break on the fixed dimension order). All content — questions, band text,
  dimension states, moves — lives in the `DIMENSIONS` / `QUESTIONS` / `BANDS` arrays in the page's
  own `<script>`. **No backend and no network calls**: scoring is pure JS, progress is kept in
  `localStorage` (`azra-readiness-v1`, wrapped in try/catch), and results leave the browser only if
  the visitor uses "Email my results", which is the same `mailto:` pattern as `contact.html`. That
  email deliberately sends the *compact* report — long `mailto:` URLs get truncated by some clients
  — while "Copy my results" copies the full one including every answer. Styled with `.assess-*` in
  `styles.css`, plus an `@media print` block so "Print or save as PDF" yields a clean report.
- Agent-discoverability layer (Phase 0 of the "agent-native" roadmap — see `agent_native.md`):
  - `llms.txt` / `llms-full.txt` — curated + full site text for LLM answer engines.
  - `services.json` / `projects.json` — machine-readable catalog of service lines and case studies.
    **Keep these in sync when service/project copy changes.**
  - `robots.txt` / `sitemap.xml` — reference `https://www.azraanalytics.com` as the canonical host.
  - Every HTML page has a JSON-LD `<script type="application/ld+json">` block in `<head>`
    (`ProfessionalService` + page-specific type). Update it if page content changes materially.
- Every page repeats the same header/nav/footer markup (no templating, since `fetch()`-based
  includes break when the site is opened via `file://` with no server — see Preview below).
  If you edit the nav or footer, **update all 8 HTML files** (the 6 originals + `faq.html`
  + `readiness.html`).
  Nav order: Home · Team · Projects · Services · Readiness · AI Tracker · FAQ · Contact.
- `styles.css` — mobile-first CSS with breakpoints at 640px (tablet) and 900px (desktop).
  Design tokens (brand colors) are CSS variables at the top of the file.
  Exception: the **nav** switches from hamburger to horizontal bar at **1000px**, not 900px —
  with eight items the row overflows a 900px viewport. Don't add a ninth nav item without
  re-checking that width.
- Fonts: Google Fonts — Space Grotesk (page headings h1/h2/h3), Inter (body). The **logo wordmark**
  is a separate choice: it's baked into the raster image using **Poppins Regular**, identified by
  comparing several candidate typefaces against the original brand-source artwork letterform by
  letterform (Space Grotesk was an earlier, incorrect guess — visibly different weight/letterforms
  from the real logo). Don't swap the logo's typeface without re-checking against `brand-source/`.
- Brand palette: green #53823b / deep green #3f6530 / cream #faf5ee / slate text #38424a (green
  matches the official logo artwork exactly — measured from the source files in `brand-source/`).
- Logo: `images/logo.png` (header, on light backgrounds) and `images/logo-light.png` (footer, on the
  dark `--slate` background) — icon + "Azra Analytics" wordmark only, built with PIL + Space
  Grotesk. The tagline "Strategy Driven AI and Data" is **real HTML/CSS text** (`.brand-tag`) next
  to the logo, not baked into the image — an earlier version baked the tagline into the raster
  image and it became unreadably small at header size, so don't repeat that. Original brand files
  (icon variants, .eps, old "Full Stack Data Science" tagline) kept in `brand-source/` for reference.
  Team photos live in `images/team/`.
- **Logo canvas geometry (measured, don't "tidy" it away).** `logo.png` is 2887x803 and its
  canvas does *not* hug the artwork, which is the root of three separate CSS compensations:
  the icon occupies x 25-441 / y 25-798 (774px tall), while the "Azra Analytics" wordmark is
  x 653-2862 / y 216-540 (only 325px tall). So: (a) there is ~1px of transparent margin on
  the left, which is why `.brand` gets `padding-left: 6px` on phones — without it the icon
  sits on the screen edge; (b) the wordmark starts 653/803 = **0.813** of the way across
  relative to the logo's height, the `--wordmark-x` token that indents `.brand-tag` so the
  tagline sits under the wordmark and not under the icon; (c) 262/803 = **0.326** of the
  height is empty canvas *below* the wordmark, the `--wordmark-bottom` token that pulls the
  tagline back up through it. Logo height is driven by a `--logo-h` variable on `.brand` /
  `.footer-brand` so the tagline offsets track it at every breakpoint — set the height there,
  never on `.brand-logo` / `.footer-logo` directly. The real visual gap is the flex `gap`
  (4px header, 6px footer). Remeasure before changing any of these numbers.
  **Trimming the canvas would NOT remove (b) or (c)** — this was checked: trimming tight to
  the ink gives 2838x774, moving `--wordmark-x` only 0.813 -> 0.811 and `--wordmark-bottom`
  only 0.326 -> 0.333. The empty space is not canvas padding, it is that a 774px-tall icon
  and a 325px-tall wordmark share one image, and trimming the outside cannot change their
  relationship. The only real fix is splitting the artwork into separate icon and wordmark
  images and composing the lockup in HTML (icon, then a column holding wordmark + tagline),
  which would touch the header and footer markup in all 8 pages. Considered and **declined
  2026-09-16** — the current CSS renders correctly and the refactor is pure internal tidiness
  with visual-regression risk. Don't redo this analysis from scratch.
- Team photo crops: `.avatar-photo` is an 88px circle using `object-fit: cover`, so a portrait
  source gets a centered square crop and can lose the top of the head. Prefer **square sources**
  so no cropping happens at all. `omar-malik.jpg` was a 2000x2660 / 1MB portrait whose head
  started only 100px down, so the centered crop sliced 236px off it; it is now a pre-cropped
  512x512 / 38KB square (face-centered, crop box (244,0)-(1677,1433) of the original, head 60%
  of frame with 7% headroom — the original's own framing allowed no more). The full-resolution
  original is in git history: `git show 0a746db:images/team/omar-malik.jpg > omar-original.jpg`.
  Ahmad's is square (1080x1080). Shahbaz's is still a 333x437 portrait — his head clears the
  default center crop, but it is 121KB for an 88px avatar and could get the same treatment.
- Preview: just open `index.html` in a browser (double-click). No server needed — this is also why
  the header/footer are duplicated per page instead of loaded via JS `fetch()`. That is the local
  check; the real one is the live site a minute after pushing, since `main` deploys automatically.

## Status / still TODO
- [ ] Omar Malik's bio text on `team.html` (title is set to "Founder & CEO" per the live site; a
      generic strategy/decision-architecture bio is in place — confirm wording with Omar).
- [x] Confirm/correct Services and Projects descriptions — pulled from the live Wix site
- [x] Add real logo image — icon + wordmark, tagline now real text (not raster)
- [x] Team photos — in `images/team/`
- [x] Real LinkedIn URL — linkedin.com/company/azra-analytics
- [x] Multi-page replication of the full live site, including the AI Adoption Tracker landing page
- [ ] Contact form has no real backend (see `contact.html` note above) — fine for now, revisit if a
      no-email-client submission flow is wanted
- [x] Deploy on Netlify and point the domain at it — **done**; live and verified 2026-09-16.
      Registrar Squarespace, DNS at Squarespace, apex `A` -> `75.2.60.5`, `www` `CNAME` ->
      `remarkable-eclair-3f9c15.netlify.app`, Google Workspace MX intact, HTTPS on both.

## "Agent-native" roadmap
Full plan: `agent_native.md` (in this repo). Turning the brochure into a site that's useful to
people in Azra's domains and to AI agents (on-site assistant + external answer engines).
- [x] Phase 0 — agent-discoverability: `llms.txt`, JSON-LD on every page, `services.json` /
      `projects.json`, `faq.html`, `robots.txt` / `sitemap.xml`. Pure static, no backend.
- [ ] Phase 1 — deterministic give-away tools (JS only):
      - [x] AI/Data Readiness Assessment (flagship) — `readiness.html`, done 2026-09-06.
      - [ ] Use-case prioritization matrix (impact × feasibility).
      - [ ] "Is this a good AI use case?" checker — better with the Phase 2 LLM backend.
      - [ ] Bring the AI Adoption Tracker in-house (`/adoption.json`) instead of linking out.
- [ ] Phase 2 — "Azra Advisor" assistant: needs ONE serverless function (Cloudflare Pages /
      Netlify) as an Anthropic API-key proxy + Turnstile + rate limit; grounded on a hand-written
      `content/knowledge.md`. This is the one open infra decision.
- [ ] Phase 3 — MCP server (Cloudflare Worker) exposing read-only tools for external agents.

## Owner
Omar Malik — omar@azraanalytics.com — GitHub: omarRmalik
