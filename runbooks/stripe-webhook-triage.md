# Runbook · Stripe webhook triage

**Last updated** · 2026-04-19
**Owner** · Omkar Jaliparthi
**Related** · [Postmortem template](../postmortems/README.md) · [ADR 0001 · Supabase](../adrs/0001-supabase-over-self-hosted-postgres.md)

---

## Symptom

One or more of:

- Stripe Dashboard shows repeated webhook delivery failures (red badges on events)
- Users report purchases that "went through" but credits/subscription state didn't update
- Support volume spikes on billing-related questions

## Severity default

**High.** Revenue is flowing but state is drifting. Every minute widens the reconciliation window.

## Blast radius

- **Customers** — anyone who checked out in the affected window (verify with Stripe event list)
- **Data** — `subscriptions`, `credit_ledger`, `chargeback_cases` tables may be out of sync with Stripe truth
- **Not affected** — customers on the PayPal rail

---

## Immediate checks · first 5 minutes

### 1. Confirm it's webhooks, not checkout

```bash
# Stripe CLI — tail live events
stripe events list --limit 20
```

If checkout sessions are being created but `customer.subscription.created` events aren't landing on our endpoint → **webhook delivery problem.** Continue.

If checkout sessions aren't being created at all → **not a webhook problem.** This runbook does not apply. See checkout-failure runbook.

### 2. Check endpoint health

```bash
curl -I https://{PROD_DOMAIN}/api/billing/webhook
# Expected: 405 Method Not Allowed (GET is rejected; endpoint is up)
# If 5xx: app is down. Go to deploy-rollback runbook.
# If timeout: platform issue. Check Vercel status.
```

### 3. Check signature verification

In Stripe Dashboard → Developers → Webhooks → production endpoint → recent deliveries:

- **HTTP 400 "signature verification failed"** → `STRIPE_WEBHOOK_SECRET` env var drift. Rotate/redeploy.
- **HTTP 500** → app error inside the handler. Go to step 4.
- **Timeouts** → handler too slow (>20s). Go to step 4.

### 4. Check server logs

```bash
# Vercel CLI
vercel logs {PROD_DEPLOYMENT_URL} --since 15m | grep -i webhook
```

Look for:
- `stripe.webhooks.constructEvent` failures → signature / secret issue
- Unhandled exceptions after signature pass → handler bug
- Slow queries → Supabase hot spot, see [Supabase migration rollback](./supabase-migration-rollback.md)

---

## Decision tree

```
Is it signature verification?
├── Yes → rotate STRIPE_WEBHOOK_SECRET in Vercel, redeploy, retry failed events
└── No
    │
    Is the handler throwing?
    ├── Yes → rollback to last known good deploy, open incident
    └── No
        │
        Is it slow (>10s)?
        ├── Yes → check DB connection pool, check for a migration in flight
        └── No → escalate (unknown failure mode)
```

---

## Recovery

### Replay missed events

Stripe retains webhook events for 30 days. Once the endpoint is healthy:

```bash
# From Stripe Dashboard → Developers → Events → filter "failed"
# Click into each event → "Resend" button
# Or bulk via API:
stripe events list --delivery-failed --limit 100 | jq '.data[].id' | xargs -I {} stripe events resend {}
```

### Reconcile state

For the affected window, for each failed event:

1. Pull event payload from Stripe API
2. Run handler idempotently against live DB
3. Verify expected state in the affected table (`subscriptions`, `credit_ledger`, etc.)
4. Log the reconciliation in the incident doc

**Idempotency is non-negotiable.** Every handler must be safe to replay. If a handler is not idempotent, stop, fix it, then replay.

---

## Rollback

If the handler is broken, the fastest recovery is to roll back the last deploy:

```bash
vercel rollback {PROD_DEPLOYMENT_URL} --yes
```

Then replay failed events against the rolled-back version.

---

## Postmortem trigger

A postmortem is required if **any** of:

- Exposure window > 15 minutes
- ≥ 1 customer contacted support for a related issue
- Any reconciliation step required manual DB writes
- Any event is unrecoverable (Stripe has lost it, or handler cannot be made idempotent)

Template · [postmortems/](../postmortems/)

---

## Hardening backlog

- [ ] Alert on webhook 4xx/5xx rate > 1% over 5 minutes
- [ ] Alert on delivery latency > 5s (early warning before timeout)
- [ ] Dashboard · Stripe events last 24h vs. DB state divergence count
- [ ] Synthetic canary · create/cancel a test subscription every hour in staging, verify webhook round-trip
