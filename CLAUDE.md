# Azra Analytics — Website Project

## What this is
A rebuild of the Azra Analytics website (currently live on **Wix** at www.azraanalytics.com)
as a **plain HTML + CSS static site** — no build tools, no Node required. The goal is a clean,
professional, **mobile-first** site the owner fully controls in code, to eventually replace the Wix site.

Company: Azra Analytics — a strategy-driven data & AI consulting firm.
Tagline: "Strategy Driven AI and Data" / "Turn your data into intelligent workflows and better decisions."

## Why we rebuilt (context)
- Original site is on Wix (classic Wix Editor). Wix does **not** allow exporting site code.
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
- Every page repeats the same header/nav/footer markup (no templating, since `fetch()`-based
  includes break when the site is opened via `file://` with no server — see Preview below).
  If you edit the nav or footer, **update all 6 HTML files**.
- `styles.css` — mobile-first CSS with breakpoints at 640px (tablet) and 900px (desktop).
  Design tokens (brand colors) are CSS variables at the top of the file.
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
- Preview: just open `index.html` in a browser (double-click). No server needed — this is also why
  the header/footer are duplicated per page instead of loaded via JS `fetch()`.

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
- [ ] Deploy free (Netlify or Cloudflare Pages) and point azraanalytics.com domain at it

## Owner
Omar Malik — omar@azraanalytics.com — GitHub: omarRmalik
