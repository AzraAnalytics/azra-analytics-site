# Making azraanalytics.com "agent-native"

A roadmap for evolving this site from a static brochure into something genuinely useful to
people working in Azra's domains — and to AI agents, both an on-site assistant and external
answer engines / procurement copilots.

Constraints to respect: no local build tooling, free hosting (Netlify / Cloudflare Pages),
non-technical owner, git-push-simple workflow.

---

## "Agent-native" means two layers, both in scope

1. **Agent-consumable** — external agents can discover, understand, and act on the site:
   `llms.txt`, JSON-LD structured data, a machine-readable service catalog, later an MCP
   endpoint.
2. **Agent-powered** — the site hosts a domain-expert assistant that produces useful
   *artifacts* (a readiness report, a use-case verdict, a draft roadmap), not just chat.

The value is real utility given away — tools + assistant — not a "book a call" bot.

---

## The one infrastructure decision

A live LLM assistant needs an API key, which cannot sit in a static site.

- **Recommended — one serverless function.** A single `functions/api/chat.js` on Cloudflare
  Pages (or Netlify Functions) that proxies to the Anthropic API. One file, deploys from
  Git, no local Node build, key is a host-dashboard secret. Add Cloudflare Turnstile + a
  simple rate limit for abuse control. Unlocks the assistant and the LLM-backed tools.
  Cost: a few dollars/month at low traffic; cap it.
- **Static-only fallback.** No function. Work stops at the discoverability layer (Phase 0)
  plus the deterministic JS tools (Phase 1). No live assistant.

Phases 0 and 1 are valid either way. Phase 2 needs the function.

---

## Phase 0 — Agent-discoverability (pure static) — DONE (2026-09-06)

- [x] `llms.txt` + `llms-full.txt` — curated map + full page text so ChatGPT / Perplexity /
      Claude / Google AI Overviews cite Azra accurately for queries like "public-health data
      infrastructure consultants" or "OCR + LLM bank-statement pipeline".
- [x] **JSON-LD** on every page: `ProfessionalService` plus page-specific types
      (`Service` ×4, `CreativeWork` ×4, `Person` ×3, `FAQPage`, `WebApplication`,
      `ContactPage`).
- [x] `services.json` + `projects.json` — machine-readable catalog. Per engagement: the
      problem it solves, deliverables, typical duration, ideal-client profile, and the proof
      project. Lets an agent match a stated need to an offering without parsing prose.
- [x] `faq.html` — 8 short, self-contained Q&A (~150 words each) on real domain questions.
      Doubles as grounding for the future assistant.
- [x] `robots.txt` + `sitemap.xml`.

**Keep in sync:** if service or project copy changes, update `services.json` /
`projects.json` / `llms-full.txt` and the relevant page's JSON-LD block.

---

## Phase 1 — Give-away tools (deterministic JS, no backend) — NOT STARTED

Each maps to a real service line and ends with "copy / send my results to Azra" (reuse the
`mailto:` pattern from `contact.html`).

- [ ] **AI / Data Readiness Assessment** (flagship lead magnet) — 12–15 questions producing a
      scored radar across data foundations, governance, talent, use-case pipeline, and
      decision culture. Deterministic scoring in JS; an LLM (if present later) writes the
      narrative and the "next 3 moves". This *is* what "Strategy and Workflow Design" sells.
- [ ] **Use-case prioritization matrix** — visitor lists candidate use cases; the tool plots
      impact × feasibility and proposes a sequence. (Strategy + Machine Learning.)
- [ ] **"Is this a good AI use case?" checker** — paste a workflow, get an automate /
      augment / leave-alone verdict against a rubric, plus the matching Azra pattern (the
      bank-statement project is the worked example). Best with the LLM backend.
      (AI Application Development.)
- [ ] **Bring the AI Adoption Tracker in-house** — replace the outbound
      `ai-adoption-tracker.onrender.com` link with an embedded view plus `/adoption.json`,
      so the assistant can cite "AI adoption in your state/industry is X% and rising" and
      external agents can query it.

---

## Phase 2 — "Azra Advisor" on-site assistant (needs the function) — NOT STARTED

- [ ] `functions/api/chat.js` proxy: injects the key + system prompt, streams the response,
      Turnstile + rate limit.
- [ ] Grounding: one hand-authored `content/knowledge.md` (methodology, the 4 service lines,
      the 4 case studies, a diagnostic rubric) stuffed into the system prompt. Small firm —
      no vector DB. Owner-editable.
- [ ] UX: a floating widget on every page plus a full-page `/advisor`. It runs a **structured
      intake** — "What decision are you trying to make better? What data exists and who owns
      it? Where does the workflow slow down?" — then produces a one-page output the visitor
      can copy or email to Azra: the decision framed, a candidate approach, which service
      line fits, and the closest case study. Every session becomes a qualified lead with
      context.
- [ ] Wire in the Phase 1 tools (the assistant can launch the assessment and interpret the
      score).

---

## Phase 3 — Agent-to-agent interface (optional, high on-brand) — NOT STARTED

- [ ] **MCP server** as a Cloudflare Worker exposing read-only tools: `get_service`,
      `search_case_studies`, `assess_readiness`, `request_intro`.
- [ ] Publish it under `/.well-known/` so a research or procurement agent can interrogate
      Azra programmatically. "We build these for clients and run one on our own site" is a
      strong proof point.

---

## Ongoing — content flywheel

- [ ] 3–5 evergreen "field notes" articles on the core domains (public-health analytics,
      decision architecture, workflow automation, AI-adoption measurement). Assistant
      grounding + GEO/SEO + credibility, compounding.

---

## Lead capture

Start with the existing `mailto:` pattern. Optional upgrade: Formspree / Netlify Forms (real
submissions, no mail-client popup), or a store (Cloudflare KV / Google Sheet) if a fuller
backend is chosen. Add a consent checkbox + a short privacy note wherever a tool sends data.

---

## Recommended sequence

1. Phase 0 — done.
2. Readiness Assessment as pure JS — strongest lead magnet, still no backend.
3. Decide on the serverless function; if yes, add it + the Azra Advisor and wire the
   assessment into it.
4. Phase 3 MCP if there's appetite.

---

## Verification per phase

- **Phase 0:** Google Rich Results test on the JSON-LD; `curl` each `.json`, `llms.txt`,
  `sitemap.xml`; open every page from `file://` and confirm nothing broke.
- **Phase 1:** open each tool page via `file://`, complete it, confirm the deterministic
  score and chart render with no network.
- **Phase 2:** `wrangler pages dev .` (or `netlify dev`) locally; confirm the streamed reply,
  a Turnstile challenge, a rate-limit rejection, and the "email results" fallback.
- **Phase 3:** run the MCP inspector against the deployed Worker; call each tool.
