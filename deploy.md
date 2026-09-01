# Deploying azraanalytics.com (moving off Wix)

This is a plain static site (no build step), so any static host works. These steps use
**Netlify** — free, no build config needed, and the simplest custom-domain setup of the free
options. **Cloudflare Pages** is an equally good alternative; see the note at the end.

## Part 1 — Deploy the site to Netlify

1. Go to netlify.com → sign up (easiest: "Sign up with GitHub" so it's already linked).
2. Click **Add new site → Import an existing project**.
3. Choose **GitHub**, authorize Netlify, and select the `AzraAnalytics/azra-analytics-site` repo.
4. On the build settings screen: leave **Build command** blank and **Publish directory** blank
   (or `.` / `/`) — there's nothing to build, it's static HTML.
5. Click **Deploy site**. Netlify gives you a temporary URL like `random-name-123.netlify.app`.
6. Open that URL and click through every page (Home, Team, Projects, Services, Contact, AI
   Tracker) — including on your phone — to confirm everything looks right before touching the
   domain.

## Part 2 — Point azraanalytics.com at Netlify

7. In Netlify: **Site settings → Domain management → Add a domain** → enter `azraanalytics.com`.
8. Figure out where your domain is actually registered — log into your Wix account →
   **Domains**. It'll say one of:
   - **"Domain bought through Wix"** (Wix is the registrar), or
   - It shows a different registrar name (GoDaddy, Namecheap, Google Domains, etc.) with DNS
     just *pointed at* Wix.
9. Depending on which:
   - **If Wix is the registrar:** you don't have to move the domain anywhere. Go to Wix →
     Domains → your domain → **DNS records / Advanced DNS**, and replace the existing records
     with the ones Netlify shows you on the "Add a domain" screen (typically one A record for
     the root domain + a CNAME for `www`).
   - **If it's registered elsewhere:** log into that registrar instead and add the same
     records there.
10. Save the DNS changes, then wait — usually 10–60 minutes, occasionally up to 24–48 hours.
11. Once DNS resolves, Netlify auto-issues a free HTTPS certificate (Let's Encrypt) — this
    happens automatically a few minutes after it detects the domain pointing correctly. No
    action needed from you.
12. Test `azraanalytics.com` and `www.azraanalytics.com` in a browser and confirm the
    padlock/HTTPS shows up.

## Part 3 — Retire Wix (only once you're confident)

13. Let the new site run for a few days and make sure everything's stable.
14. In Wix, cancel/downgrade the **website plan** (Settings → Billing) — but do **not** cancel
    the domain itself if Wix is your registrar; you still need that domain registration to
    stay active, it just won't be hosting the site anymore.

## Alternative: Cloudflare Pages

Same overall flow — connect the GitHub repo (Cloudflare Pages → Create a project → Connect to
Git), no build command / output directory left as root, deploy, then add
`azraanalytics.com` as a custom domain in the project's settings and update DNS the same way
as Part 2. Cloudflare Pages works best if you're willing to move the domain's nameservers to
Cloudflare, but a CNAME/A-record setup without changing nameservers also works.

## After DNS changes are made

Ask Claude to check DNS propagation with a lookup command before assuming it's live — don't
just wait and hope.
