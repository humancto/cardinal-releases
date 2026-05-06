# Cardinal — distribution & analytics (free product)

Cardinal is free for now. No payment, no email gate, no tax forms, no
storefront. Just a download link on a landing page. The analytics
question is: how do you know how many people are downloading and where
they're coming from?

## What's already there (zero setup)

### GitHub Release download counts

Every release asset has a `download_count` you can query free:

```sh
# Raw counts for the latest release
gh api repos/humancto/cardinal-releases/releases/latest \
  --jq '.assets[] | {name, download_count}'

# All releases, totalled per asset name (handy when you want
# "Cardinal-latest.dmg" downloads across the v0.x train)
gh api repos/humancto/cardinal-releases/releases \
  --jq '[.[].assets[] | {name, count: .download_count}]
        | group_by(.name)
        | map({name: .[0].name, total: (map(.count) | add)})'
```

This counts every actual binary fetch from GitHub. It does NOT distinguish
unique users (a single user re-downloading is N, not 1) and it does NOT
include people who clicked the button but cancelled the download. For
funnel analysis you want a landing-page tracker on top.

Sample query for a quick check:

```sh
gh api repos/humancto/cardinal-releases/releases --jq \
  '[.[].assets[] | select(.name | startswith("Cardinal-v")) | {tag: .name, count: .download_count}]'
```

## Optional: landing-page analytics

Pick **one** of these. All three are privacy-friendly (no cookies, no PII,
GDPR-clean by design — meaning you don't have to add a cookie banner).

### Option A — Plausible Analytics ($9/mo)

The slickest. Real-time dashboard, outbound-link tracking (you can see
how many people clicked "Download" without going to GitHub), referrer
breakdown, geography.

**Setup (4 min):**

1. Sign up at <https://plausible.io>, free 30-day trial.
2. Add domain: `humancto.github.io/cardinal-releases` (or `cardinal.app`
   when you point a real domain at GitHub Pages).
3. Plausible gives you a script tag like:
   ```html
   <script
     defer
     data-domain="humancto.github.io"
     src="https://plausible.io/js/script.js"
   ></script>
   ```
4. Paste it inside `<head>` in `index.html`. Commit + push.

**Outbound click tracking** (one extra step, lets you see "Download"
button clicks):

1. In Plausible dashboard → settings → Goals → "Outbound link clicks".
2. Add this to the script:
   ```html
   <script
     defer
     data-domain="humancto.github.io"
     src="https://plausible.io/js/script.outbound-links.js"
   ></script>
   ```

### Option B — GoatCounter (free for personal use)

Lighter than Plausible, generous free tier.

**Setup (4 min):**

1. Sign up at <https://www.goatcounter.com>, pick a subdomain like
   `cardinal.goatcounter.com`.
2. Get a script tag like:
   ```html
   <script
     data-goatcounter="https://cardinal.goatcounter.com/count"
     async
     src="//gc.zgo.at/count.js"
   ></script>
   ```
3. Paste in `index.html`. Commit + push.

### Option C — Fathom Analytics ($14/mo)

Equivalent to Plausible. Slightly better-looking dashboard. Same setup
shape — one script tag in `<head>`.

### Option D — Self-host (free, more setup)

If $9/mo bothers you: Umami or Plausible Community Edition on Cloudflare
Workers / Vercel / a $5 droplet. Adds maintenance overhead. Not worth it
for "I want to know if 50 people downloaded today."

## Recommendation

For "free product, just want to know download numbers": **Option A
(Plausible)** when you're ready, **GoatCounter** if you want $0/mo.

Until then, `gh api ... download_count` is enough — every Cardinal release
already has it, no setup needed.

## What about email collection?

Deferred until paid. When you flip to a price (post-v1.0), revisit
Gumroad — at that point the tax info dance is worth it because you're
actually collecting payments, and Gumroad handles licensing + receipts +
VAT for you.

Until then: people who want updates will see them via Sparkle (already
wired). People who want to be notified manually can email you. No email
list needed at this stage.

## When you flip to paid

Estimated path (not a runbook, just the shape):

1. Pick a price in Gumroad ($X). Tax info required at that point (US:
   W-9; non-US: W-8BEN).
2. Gumroad redirect URL stays the same (`releases/latest/download/...`).
3. Cloudflare Worker added in front of `cardinal-releases/appcast.xml`
   to verify a Gumroad license key before serving — gates auto-update on
   payment.
4. Existing v0.x users grandfathered to free auto-update (or migrated
   via a one-time email).
