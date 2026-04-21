# ADR 0001 · Supabase over self-hosted Postgres

**Status** · Accepted · 2026-02-12
**Author** · Omkar Jaliparthi
**Program** · Insights by Omkar

---

## Context

The SaaS needed a primary datastore for users, content library, credits, subscriptions, chat sessions, admin state, and ~30 other domain tables. Three baseline requirements:

1. Row-level security enforced at the DB layer — not app code — for multi-tenant content
2. Realtime for admin dashboards
3. A small team (one person) should not be running pg_hba.conf or a replication topology

## Options

### A · Self-hosted Postgres on a small VPS

- Full control, lowest unit cost
- Requires backups, monitoring, upgrades, failover, connection pooling done manually
- RLS + realtime + auth all hand-built

### B · Managed Postgres (RDS / Cloud SQL / Neon)

- Backups and upgrades handled; still need to ship auth, RLS helpers, realtime separately
- Two to three vendors instead of one
- Medium operational surface

### C · Supabase

- Managed Postgres with RLS, auth, storage, realtime, edge functions as one platform
- Vendor lock-in, but the underlying DB is still Postgres — exit cost is bounded
- Free tier for pre-revenue; paid tier priced for a solo product

## Decision

**Option C.** For a one-person team shipping a multi-tenant product, operational hours matter more than marginal unit cost.

## Consequences

**Accepted:**
- Cannot tune autovacuum or pg parameters freely
- Connection limits require pgBouncer discipline
- Some Postgres extensions are gated by the platform

**Won:**
- RLS contracts live next to the schema, reviewed as a unit
- Realtime for admin dashboards is a channel subscription, not a service to build
- Migrations ship via `supabase db push` as part of normal deploy

## Exit criteria

Revisit this ADR if any hold:

- Storage exceeds 100 GB in the primary DB
- p95 query time exceeds budget under real load for reasons that tuning would fix
- RLS policy surface exceeds what the platform can express cleanly
- Vendor pricing changes materially against the workload

## Related

- [RFC · Dual Payment Rails](../rfcs/dual-payment-rails.md) — uses the same DB for billing state
- [Runbook · Supabase Migration Rollback](../runbooks/supabase-migration-rollback.md)
