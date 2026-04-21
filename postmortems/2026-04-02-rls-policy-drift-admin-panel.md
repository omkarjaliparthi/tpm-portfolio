# Postmortem · RLS policy drift caused empty admin panel

**Date** · 2026-04-02
**Severity** · Medium · operator-facing, no customer impact
**Author** · Omkar Jaliparthi
**Status** · Resolved

---

## Summary

After a migration that reorganized roles, the admin panel rendered as empty for the primary operator account for roughly 25 minutes. No customer-facing surface was affected. Root cause was a Row-Level Security policy that had been silently narrowed by a column rename during the migration — the policy now checked against a column that no longer had a matching role value for the admin user.

## Impact

- **Customers affected** — zero
- **Operators affected** — one (primary admin account); no other admin accounts exist yet
- **Data loss** — none; the data was always readable at the Postgres level, just hidden by policy
- **Revenue** — none

## Timeline · UTC

| Time | Event |
|---|---|
| 18:02 | Migration applied; adds a new `role_v2` column, re-points existing policies |
| 18:03 | App deploy ships with code that assumes `role_v2` populated for admin |
| 18:05 | Admin panel loaded · tables render as "0 rows" |
| 18:05 | First reflex: check app logs — no errors. API returns 200 with empty arrays. |
| 18:10 | Suspicion shifts to RLS · `psql` as `service_role` returns rows. `psql` as authenticated admin returns zero. |
| 18:18 | Policy diff spotted · the migration renamed `role` → `role_v2` on some policies, left `role` on others; admin account had `role` set but not `role_v2` |
| 18:24 | Backfill run to copy `role` into `role_v2` for the admin account |
| 18:27 | Admin panel renders correctly · rollback not needed |

## Root cause

The migration was written in two parts · add `role_v2`, then update all `FOR SELECT USING` policies to reference it. Between those two parts, the backfill that should have populated `role_v2` from `role` was missing. A reviewer (me) didn't catch it because I tested the migration in staging against a freshly-seeded database where `role_v2` was populated by the seed script, not by the backfill I forgot to include.

## Contributing factors

1. **Staging seed masked the missing backfill.** Seed populated the new column directly; migration-only run did not. The production environment exposed the omission.
2. **No explicit "migration against a prod-like snapshot" step.** Staging database shape was right; staging data was not representative.
3. **RLS failures return zero rows, not errors.** A policy that evaluates to `false` against a real user simply hides data. From the app's perspective, "no rows" looks identical to "this user has no data."

## What went well

- **No customer impact.** The narrowed policy affected admin reads only.
- **Root cause identified in under 15 minutes.** The heuristic "if API returns 200 with empty arrays after a migration, suspect RLS first" paid off.
- **Forward fix worked.** A backfill was cleaner than reverting the migration.

## What went badly

- **The migration should not have left prod in a half-state.** Backfill belongs *in* the migration, not as a follow-up.
- **Staging environment did not reflect production data shape.** Seed-populated staging hides the class of bugs where a migration is correct but its data-migration is missing.
- **Observability gap.** No dashboard surfaces "admin account can or can't see expected tables" as a health signal.

## Corrective actions

| Action | Owner | Due | Status |
|---|---|---|---|
| Backfill `role_v2` included in the migration itself (retroactively rolled up) | Omkar | 2026-04-02 | ✅ Done |
| Staging data pipeline · periodic sanitized snapshot from prod, not synthetic seed | Omkar | 2026-04-19 | 📅 Queued |
| Pre-migration checklist · "does this require a data backfill? is it in the migration?" | Omkar | 2026-04-02 | ✅ Done |
| Admin panel smoke test · synthetic admin account loads each major table, asserts >0 rows where expected | Omkar | 2026-04-21 | 📅 Queued |

## Lessons

1. **Schema migrations without data migrations are half-done.** If the app code depends on a new column being populated, the migration must populate it — not a follow-up script.
2. **RLS makes empty states ambiguous.** "No rows" now has two meanings: *no data* or *no access*. Health checks must distinguish them.
3. **Test environments that diverge from production shape will hide the exact bugs you need them to catch.** Prefer sanitized prod snapshots over synthetic seeds for any environment that serves as the last checkpoint before production.
4. **Reviewers who wrote the code are weak reviewers.** A second pass with fresh eyes — even a time-delayed second pass by the same author — catches more than immediate self-review.
