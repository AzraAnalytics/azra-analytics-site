# Deploying azraanalytics.com — moving from Wix to Netlify

This guide is written for a non-technical person doing this for the first time. Every step
says **what you're doing and why**, then **exactly what to click**. Take it slowly; there is
no rush and nothing here is hard to undo.

---

## The situation (read this first)

We checked how `azraanalytics.com` is set up today. Three separate things are involved, and
they live in three different places:

| Thing | Where it lives right now | What we're doing with it |
| --- | --- | --- |
| **Domain registration** (who owns the name) | **Squarespace** — this is your registrar | Leaving it exactly where it is |
| **DNS** (the phone book that says where the name points) | **Wix** — nameservers are `ns0.wixdns.net` / `ns1.wixdns.net` | **Moving this control to Squarespace** |
| **Website** (the pages people see) | **Wix** | **Moving this to Netlify** (free) |
| **Email** — `omar@azraanalytics.com` | **Google Workspace** (5 "MX" records currently stored inside Wix's DNS) | Keeping it working — we re-enter those mail records at Squarespace |

**The one thing that can go wrong: email.** Your email records currently sit inside Wix's
DNS. When we take DNS control away from Wix, those records do **not** move automatically. If
they're forgotten, `omar@azraanalytics.com` stops receiving mail. This whole guide is built
around not letting that happen.

Good news: you have **no SPF / DKIM / DMARC records** set up, so the *only* email thing to
carry over is the **5 Google MX records**. That's it.

The end state: domain at Squarespace, DNS at Squarespace, website on Netlify, email still on
Google. Wix is completely out of the picture.

**Rough timeline:** ~30–45 minutes of clicking, then a wait of anywhere from 1 hour to 48
hours (usually a few hours) for the internet to catch up, then 10 minutes of checking.

---

## What you'll need open

- Your **Wix** login (`manage.wix.com`)
- Your **Squarespace** login (`account.squarespace.com`)
- Your **Netlify** login (make one at `netlify.com` — "Sign up with GitHub" is easiest)
- Your **Google Admin** login (`admin.google.com`) — only to double-check mail settings
- A notepad (paper or a text file) to write down two values as you go

---

## STEP 0 — Back up your current DNS (5 minutes, do not skip)

**Why:** Once we switch DNS control, the old settings at Wix become invisible. A screenshot
now means you can always see exactly what was there.

**Do this:**

1. Go to `manage.wix.com` and sign in.
2. Open your site, then go to **Settings** (bottom-left) → **Domains**.
3. Click `azraanalytics.com`, then look for **"Advanced"**, **"Edit DNS"**, or
   **"DNS Records"**.
4. You'll see a list of records (types like `A`, `CNAME`, `MX`, `TXT`, `NS`).
   **Take a screenshot of the whole list.** Scroll down and screenshot again if it doesn't
   all fit. Save these somewhere you'll find them.
5. In particular, find the rows of type **`MX`**. There should be five, all pointing at
   Google addresses ending in `aspmx.l.google.com`. These are your email. You'll re-enter
   them later, so make sure they're clearly visible in your screenshot.

**What just happened:** nothing changed. You only made a record of the current setup.

---

## STEP 1 — Put the website on Netlify

**Why:** Before pointing the domain anywhere new, the new website needs to already exist and
work at a temporary address. Then switching the domain is just a redirect.

**Do this:**

1. Go to `netlify.com` and sign up / log in. Choose **"Sign up with GitHub"** so Netlify can
   see your code repository automatically.
2. Click **Add new site → Import an existing project**.
3. Choose **GitHub**, authorize Netlify if it asks, and pick the
   **`AzraAnalytics/azra-analytics-site`** repository.
4. On the build settings screen:
   - **Build command:** leave **blank**
   - **Publish directory:** leave **blank** (or type `.`)
   - There is nothing to "build" — this is plain HTML, Netlify just serves the files as-is.
5. Click **Deploy site**. Wait about a minute.
6. Netlify gives you a temporary address like **`bright-otter-1a2b3c.netlify.app`**.
   **Write this down** — you need it in Step 3. This is your "Netlify subdomain".
7. Open that address in your browser. Click through **every page**: Home, Team, Projects,
   Services, AI Tracker, FAQ, Contact. Check it on your phone too. Fix anything that looks
   wrong now, before the domain is involved.

**What just happened:** the new site is live on the internet at a Netlify address. The real
domain still points at Wix — visitors see no change yet.

---

## STEP 2 — Tell Netlify your real domain name

**Why:** Netlify needs to know it should answer for `azraanalytics.com`, and it will then
tell you the exact DNS records to create.

**Do this:**

1. In Netlify, open your site → **Site configuration → Domain management**
   (older menus call it **Domain settings**).
2. Click **Add a domain** (or **Add custom domain**). Type `azraanalytics.com`. Click
   **Verify**, then **Add domain**.
3. Netlify automatically adds **both** `azraanalytics.com` **and** `www.azraanalytics.com`.
4. Netlify shows a notice like **"Awaiting External DNS"** or **"Check DNS configuration"**.
   Click it. It shows you the records to create. **Write these two things on your notepad:**
   - An **A record** for the bare domain → IP address **`75.2.60.5`**
   - A **CNAME record** for **`www`** → your Netlify subdomain from Step 1
     (e.g. `bright-otter-1a2b3c.netlify.app`)

   > If Netlify shows a *different* IP than `75.2.60.5` (it sometimes does for special
   > plans), **use the number Netlify shows you**, not the one printed here.

**What just happened:** Netlify is now ready and waiting. Nothing points at it yet. The site
is still served by Wix.

---

## STEP 3 — Move DNS control to Squarespace and add the records

This is the real switch. **Do the whole step in one sitting.** The moment you switch, you
want the new records already going in, so there's no gap — especially for email.

Have these ready before you start:
- Your Netlify subdomain (from Step 1)
- Your Step 0 screenshot with the 5 MX records visible

### 3a — Switch the nameservers to Squarespace

**Why:** "Nameservers" decide which company's DNS is in charge. Right now it's Wix. We're
handing that job to Squarespace, where your domain already lives.

**Do this:**

1. Go to `account.squarespace.com` → **Domains** → click **azraanalytics.com**.
2. Find the **DNS** area (may be a **DNS** tab, or **DNS Settings**, or under **Advanced
   Settings**).
3. Look for the **Nameservers** section. It currently shows **custom / third-party
   nameservers**: `ns0.wixdns.net` and `ns1.wixdns.net`.
4. Choose the option to **"Use Squarespace nameservers"** (also worded as "Reset to
   default" or turning **off** custom nameservers). Squarespace fills in its own nameserver
   names for you. **Save / confirm.**

**What just happened:** you told the internet "ask Squarespace, not Wix, where this domain
points." This change takes a few hours to fully spread. During that time some visitors still
reach Wix, some reach Squarespace — which is why the next part matters.

### 3b — Delete Squarespace's placeholder records

**Why:** Squarespace auto-creates "parking page" records. If you leave them, the domain will
briefly show a blank Squarespace page instead of your site.

**Do this:**

1. Still on the Squarespace **DNS** page, find the **Custom Records** list.
2. **Delete** any record that is:
   - type **`A`**, host **`@`**, pointing to a Squarespace IP
     (numbers like `198.185.159.144`, `198.185.159.145`, `198.49.23.144`, `198.49.23.145`)
   - type **`CNAME`**, host **`www`**, pointing to `ext-cust.squarespace.com`
3. Leave any record Squarespace labels as required for the domain itself.

### 3c — Add your 7 records

**Why:** two of these send the website to Netlify; five keep your Google email working.

**Do this:** click **Add Record** for each row below. For **Host**, type `@` for the bare
domain. (If Squarespace won't accept `@`, leave Host blank instead.)

**Add the MX (email) records FIRST:**

| Type | Host | Priority | Value |
| --- | --- | --- | --- |
| MX | `@` | `1` | `aspmx.l.google.com` |
| MX | `@` | `5` | `alt1.aspmx.l.google.com` |
| MX | `@` | `5` | `alt2.aspmx.l.google.com` |
| MX | `@` | `10` | `alt3.aspmx.l.google.com` |
| MX | `@` | `10` | `alt4.aspmx.l.google.com` |

Check each value against your Step 0 screenshot — they must match exactly.

**Then add the two website records:**

| Type | Host | Value |
| --- | --- | --- |
| A | `@` | `75.2.60.5` |
| CNAME | `www` | `your-site-name.netlify.app` (your Netlify subdomain) |

**Optional but recommended** — a basic anti-spoofing record for your email, which you don't
currently have:

| Type | Host | Value |
| --- | --- | --- |
| TXT | `@` | `v=spf1 include:_spf.google.com ~all` |

> If Squarespace rejects a CNAME or MX value, try adding a dot on the end
> (`your-site-name.netlify.app.`). Some fields want the trailing dot; some add it for you.

**What just happened:** Squarespace is now the source of truth for this domain. It sends web
visitors to Netlify and email to Google. Wix is no longer referenced anywhere.

---

## STEP 4 — Wait, then check everything

**Why:** DNS changes spread across the internet gradually. Checking too early gives
misleading results.

**Do this:**

1. Wait **at least 1–2 hours** (can be up to 48; a few hours is typical).
2. **Ask Claude to run the DNS checks.** Claude will confirm, from the command line:
   - nameservers now point to Squarespace (not `wixdns.net`)
   - `azraanalytics.com` → `75.2.60.5`
   - `www.azraanalytics.com` → your Netlify site
   - MX records still point to Google — **email is safe**
3. **Test email both ways:** send a message *to* `omar@azraanalytics.com` from another
   account, and *reply from* `omar@azraanalytics.com`. Both must work.
4. If something's off after 48 hours, re-open the Squarespace DNS page and compare it
   carefully to the tables above.

**What just happened:** you verified the switch actually took effect everywhere, and that
email survived.

---

## STEP 5 — Turn on HTTPS and pick the main address

**Why:** browsers show "Not secure" without HTTPS. Netlify does this for free, automatically,
once it sees the domain pointing at it.

**Do this:**

1. Back in Netlify → **Domain management**. Once DNS resolves to Netlify (Step 4), Netlify
   requests a free **Let's Encrypt** certificate on its own. Within an hour the domain
   status changes from "pending" to showing a green padlock / "HTTPS enabled". No action
   needed — just check that it happened.
2. On that same screen, set your **primary domain**. Pick one:
   - `www.azraanalytics.com` (traditional), or
   - `azraanalytics.com` (shorter)

   Netlify automatically **redirects the other one** to your choice.
3. In a browser, visit **both** `azraanalytics.com` and `www.azraanalytics.com`. Each should
   land on the secure `https://` version of the one you picked, showing your new site.

**What just happened:** the site is fully live on your real domain, secured, with both
"www" and "no-www" working.

---

## STEP 6 — Retire Wix (only after a few solid days)

**Why:** give yourself a safety margin. If anything's wrong, you want Wix still there as a
reference.

**Do this:**

1. Let the new site and email run for **several days**. Keep using email normally and watch
   for anything missing.
2. Once you're confident: in Wix, go to **Settings → Billing** and **cancel / downgrade the
   website plan**.
3. You do **not** need to do anything about the domain in Wix — Wix was never your registrar.
   Your domain is safe at Squarespace, and its DNS is at Squarespace now too.

**What just happened:** you're fully off Wix, paying nothing for hosting, and in control of
the site in code.

---

## If switching nameservers feels like too much at once (smaller alternative)

You can split this into two calmer changes. **Only works while your Wix plan is still active
enough to let you edit DNS.**

1. Do **Steps 0, 1, 2** as above.
2. Instead of Step 3, go into **Wix's** DNS page and change just two records:
   - the **`A`** record on host `@` → `75.2.60.5`
   - the **`CNAME`** on host `www` → `your-site-name.netlify.app`
   - **leave the 5 MX records exactly as they are** — email is untouched.
3. Do **Steps 4 and 5**. The website is now on Netlify; DNS and email still run through Wix.
4. Later, as a separate relaxed task, do **Step 3** (move DNS to Squarespace, re-enter the
   MX + Netlify records) and then **Step 6**.

Trade-off: you stay dependent on Wix's DNS until you finish the move, so you can't cancel Wix
yet.

---

## Alternative host: Cloudflare Pages

Netlify is the recommendation, but Cloudflare Pages is just as good and also free. The flow
mirrors Steps 1–2: **Workers & Pages → Create → Pages → Connect to Git**, pick the repo, no
build command, output directory left as the root, deploy. Then add `azraanalytics.com` as a
custom domain in the project and do the same Squarespace DNS work in Step 3 (Cloudflare will
show you its own target values to use instead of Netlify's `75.2.60.5` / `*.netlify.app`).

---

## Reference — what "done" looks like

Ask Claude to run these; the answers should be:

- `nslookup -type=NS azraanalytics.com` → Squarespace nameservers (no `wixdns.net`)
- `nslookup azraanalytics.com` → `75.2.60.5`
- `nslookup -type=CNAME www.azraanalytics.com` → `your-site-name.netlify.app`
- `nslookup -type=MX azraanalytics.com` → five `aspmx.l.google.com` results
- Both `https://azraanalytics.com` and `https://www.azraanalytics.com` load the new site with
  a padlock.
