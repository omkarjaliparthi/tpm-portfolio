# Pre-Launch Checklist — Insights by Omkar

**Context:** Launch readiness checklist used before flipping `insightsbyomkar.com` from staging to production (v1.4.1 → v2.0). Reproduced here with environment-specific values redacted; structure preserved.

This is a **real artifact** from a shipped program, not a template. It demonstrates the scope of things a TPM needs to verify before go-live on a production consumer SaaS with payments, AI, and compliance surfaces.

---

## 1. Environment variables

Set across **Production**, **Preview**, **Development** scopes on the hosting platform.

### Core site
- `NEXT_PUBLIC_SITE_URL`, `NEXT_PUBLIC_APP_URL`, `SITE_URL`
- `DATA_ENV` — `production`

### Supabase (auth + data)
- `NEXT_PUBLIC_SUPABASE_URL`
- `NEXT_PUBLIC_SUPABASE_ANON_KEY`
- `SUPABASE_SERVICE_ROLE_KEY` — **server-only**, never in client bundle

### AI providers
- `OPENAI_API_KEY`, `ANTHROPIC_API_KEY`
- Per-module model env vars (`OPENAI_TAROT_MODEL`, `OPENAI_DREAM_MODEL`, …)

### Payments (Stripe + PayPal)
- `STRIPE_SECRET_KEY`, `NEXT_PUBLIC_STRIPE_PUBLISHABLE_KEY`
- `STRIPE_WEBHOOK_SECRET` (+ secondary if endpoints split)
- Subscription price IDs (Lucky Pro Monthly/Annual, Lucky Max Monthly/Annual)
- `PAYPAL_CLIENT_ID`, `PAYPAL_CLIENT_SECRET`, `PAYPAL_MODE=live`, `PAYPAL_WEBHOOK_ID`

### Email (Resend)
- `RESEND_API_KEY`, `RESEND_FROM_EMAIL`, `RESEND_INBOUND_DOMAIN`
- Sender-domain + inbound-domain matched

### Auth / session
- `NEXTAUTH_SECRET`, `GHOST_COOKIE_SECRET` — 32+ char random

### Cron / ops
- `CRON_SECRET` — matches the header Vercel Cron sends

### Google (Calendar + Search Console)
- Service account email + private key (PEM, `\n`-escaped)

### Analytics / monitoring
- `NEXT_PUBLIC_GA_MEASUREMENT_ID`, `SENTRY_DSN`

---

## 2. Stripe dashboard

- [ ] Webhook endpoint registered, signing secret matches env
- [ ] 4 subscription prices confirmed (IDs match env)
- [ ] Customer portal branded + cancellation flow configured

## 3. PayPal dashboard

- [ ] Live app created; client ID + secret in env
- [ ] Webhook endpoint + events: `PAYMENT.CAPTURE.COMPLETED`, `BILLING.SUBSCRIPTION.*`
- [ ] Webhook ID copied to env

## 4. Supabase

- [ ] Production project; all migrations applied
- [ ] Row Level Security enabled on every user-scoped table
- [ ] Service role key restricted server-side
- [ ] Daily backup enabled
- [ ] Auth providers enabled (email + social if shipped)
- [ ] Branded email templates uploaded

## 5. Domain + DNS

- [ ] Apex A record → hosting IP
- [ ] `www` CNAME → platform hostname
- [ ] `https://` enforced; `www` redirect to apex
- [ ] MX records for inbound email domain

## 6. Email authentication (Resend)

- [ ] Domain verified
- [ ] **SPF:** `v=spf1 include:_spf.resend.com ~all`
- [ ] **DKIM:** CNAME records from Resend
- [ ] **DMARC:** `v=DMARC1; p=quarantine; rua=mailto:...`
- [ ] Sandbox → live mode switched
- [ ] Warm-up plan if new sending domain

## 7. Cron jobs

Verify platform has each cron enabled and signed with `CRON_SECRET`:

- [ ] `/api/cron/content-generate` — daily 06:00 UTC
- [ ] `/api/cron/content-publish` — daily 14:00 UTC
- [ ] `/api/cron/intelligence` — weekly Mon 08:00 UTC
- [ ] `/api/cron/monthly-report` — 15th 10:00 UTC
- [ ] `/api/cron/re-engagement` — daily 15:00 UTC

## 8. Analytics + Search

- [ ] GA4 property created + measurement ID in env
- [ ] Google Search Console → both apex + www verified
- [ ] Sitemap submitted
- [ ] Bing Webmaster Tools import from GSC
- [ ] IndexNow key file uploaded to public root

## 9. Monitoring + errors

- [ ] Sentry project + DSN in env
- [ ] Release tag wired to commit SHA
- [ ] Platform analytics + speed insights enabled
- [ ] Uptime monitor pinging `/api/health` every 1 min

## 10. Legal + content

- [ ] `/privacy`, `/terms`, `/refund-policy` reviewed + accurate
- [ ] Cookie consent copy matches trackers that actually fire
- [ ] Statement descriptor on Stripe set to recognizable string

## 11. Assets

- [ ] Avatar + OG fallback image in `/public`
- [ ] Favicon set (app icon + apple-touch-icon)
- [ ] `robots.txt` allows production crawling

## 12. Pre-flight smoke tests *(run against production URL)*

- [ ] Sign up → email verification → sign-in works
- [ ] Purchase flow → Stripe webhook → credits appear → refund reverses
- [ ] PayPal flow end-to-end identical parity check
- [ ] One reading per module executes, saves, renders unique OG
- [ ] Subscription cancel in customer portal → webhook downgrades
- [ ] Inbound support email → routes correctly via Resend inbound
- [ ] Reduced-motion OS setting → chambers render static fallback
- [ ] Mobile smoke (iOS + Android)
- [ ] Error boundary test — force error, confirm fallback UI

## 13. Day-of-launch

- [ ] Flip Stripe test → live keys
- [ ] Flip PayPal `MODE` to `live`
- [ ] `robots.txt` to `Allow: /` if staging had it disallowed
- [ ] Socials announcement scheduled
- [ ] Sentry + deploy logs watched for first hour

---

## How to use this

**If you're a TPM shipping a new product:** start with this structure, tune section ordering by your stack.

**If you're hiring one:** use it to gauge whether a candidate has *actually* shipped production software or only described the process.

**Redaction note:** environment-specific values, internal URLs, Slack channels, and on-call rotations are removed. Structure and item list are authentic.
