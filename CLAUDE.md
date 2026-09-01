# Azra Analytics — Website Project

## What this is
A rebuild of the Azra Analytics website (currently live on **Wix** at www.azraanalytics.com)
as a **plain HTML + CSS static site** — no build tools, no Node required. The goal is a clean,
professional, **mobile-first** site the owner fully controls in code, to eventually replace the Wix site.

Company: Azra Analytics — a strategy-driven data & AI consulting firm.
Tagline: "Strategy-Driven Data & AI" / "Turn your data into intelligent workflows and better decisions."

## Why we rebuilt (context)
- Original site is on Wix (classic Wix Editor). Wix does **not** allow exporting site code.
- Owner wanted Claude to directly edit styling and make it mobile-friendly, which isn't possible
  inside Wix. So we started a fresh code-based site from the existing content instead.
- Owner is non-technical; keep things simple. No Node/npm on the original machine (Git + Python are installed).

## Tech / structure
- `index.html` — single-page site: header, hero, Services (4 cards), Projects (3 cards),
  Team (3 members), Contact CTA, footer. Vanilla JS for the mobile hamburger menu.
- `styles.css` — mobile-first CSS with breakpoints at 640px (tablet) and 900px (desktop).
  Design tokens (brand colors) are CSS variables at the top of the file.
- Fonts: Google Fonts — Space Grotesk (headings), Inter (body).
- Brand palette: green #4d7c3a / deep green #3a5c2b / cream #faf5ee / slate text #38424a.
- Preview: just open `index.html` in a browser (double-click). No server needed.

## Status / still TODO
- [ ] Real team bios & job titles (currently placeholder text for Ahmad Jamal Khan & Shahbaz Khan; Omar Malik = Founder & Principal placeholder)
- [ ] Confirm/correct Services and Projects descriptions (written from the live Wix site, may need edits)
- [ ] Add real logo image (currently styled text "Azra Analytics")
- [ ] Real LinkedIn URL (footer currently points to generic linkedin.com)
- [ ] Optional: team photos, "AI Adoption Tracker" page/feature that exists on the Wix nav
- [ ] Deploy free (Netlify or Cloudflare Pages) and point azraanalytics.com domain at it

## Owner
Omar Malik — omar@azraanalytics.com — GitHub: omarRmalik
