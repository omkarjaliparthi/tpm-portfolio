# Postmortem · Credit refund double-credit under idempotency-window edge

**Date** · 2026-03-30
**Severity** · High · potential revenue leak, caught before impact
**Author** · Omkar Jaliparthi
**Status** · Resolved

---

## Summary

A refund path that credited the user back for a failed AI generation could, under a narrow race, credit the user twice. Specifically: if the generation failed *and* the retry also failed *and* both failures hit the refund path within the idempotency window, the ledger accepted both credits. Discovered during a self-audit; zero customer complaints; the fix shipped before any customer triggered the race in production.

## Impact

- **Customers affected** — zero confirmed in production
- **Ledger entries** — a staging replay produced the double-credit reliably
- **Revenue exposure** — bounded by per-request credit cost, but cumulatively significant if left in place
- **Trust** — internal, no customer notice

## Timeline · UTC

| Time | Event |
|---|---|
| Day 0 | Self-audit of refund paths triggered by a general "re-read the ledger code" review |
| Day 1 · 14:20 | Race identified by reading the code; reproduced in staging with a scripted retry collision |
| Day 1 · 15:40 | Fix written — lift idempotency key from the *request* to the *failed generation id* |
| Day 1 · 16:05 | Fix deployed to staging |
| Day 1 · 16:55 | Staging replay no longer reproduces the double-credit |
| Day 2 · 09:30 | Fix deployed to production as a patch release |
| Day 2 · 10:15 | Scan over the last 30 days of refund ledger · no production occurrence found |

## Root cause

The refund handler used the **request id** as its idempotency key. Two *different* refund requests for the *same* underlying failed generation therefore had *different* keys. The intended invariant — "one failed generation can be refunded at most once" — was never enforced at the key.

The fix was to change the key from request-scoped to generation-scoped: `refund:{failed_generation_id}`. A second refund for the same failed generation is now rejected as a duplicate.

## Contributing factors

1. **Idempotency keys inherited from the request layer.** Request-scoped keys are fine for single-mutation endpoints; they are wrong for "compensate for a prior event" endpoints.
2. **No ledger-level uniqueness constraint.** The database would accept both rows because no unique constraint named the correct natural key.
3. **Test coverage tested the happy path.** A "refund runs once" test existed; a "two refund calls against the same generation" test did not.

## What went well

- **Caught in audit before a customer hit it.** This is the value of scheduled self-reads of high-risk code.
- **Staging replay reproduced reliably.** The bug was not left as "a theoretical race" — it was demonstrated, fixed, and demonstrated-fixed.
- **Fix deployed in hours, not days.** Small cadence enables small fixes.

## What went badly

- **The invariant was never written down.** "Exactly one refund per failed generation" is the kind of rule that belongs as a DB constraint *and* a test, not just a convention.
- **Ledger tables lacked uniqueness on natural keys.** Relied on app logic to prevent duplicates.

## Corrective actions

| Action | Owner | Due | Status |
|---|---|---|---|
| Change idempotency key to `refund:{failed_generation_id}` | Omkar | 2026-03-30 | ✅ Done |
| Add `UNIQUE (type, source_id)` constraint on credit_ledger for compensating entries | Omkar | 2026-03-31 | ✅ Done |
| Add regression test · two refund calls, same source, one succeeds one is rejected | Omkar | 2026-03-31 | ✅ Done |
| Audit all compensating flows for the same pattern (refunds, credit adjustments, retroactive grants) | Omkar | 2026-04-07 | ✅ Done |

## Lessons

1. **Idempotency keys should name the thing, not the call.** The key encodes the invariant: "at most one of these exists."
2. **Application-layer invariants deserve database constraints.** A unique index is cheap and turns a class of bugs into a 23505 that the app must handle explicitly.
3. **Self-audits are not optional for financial paths.** Scheduled re-reads of ledger code are a form of ongoing QA. Bugs found in audit are cheaper than bugs found in production.
4. **Compensating actions are their own category.** Refunds, credits, reversals — the design rules differ from regular writes because they only exist to undo a prior event.
