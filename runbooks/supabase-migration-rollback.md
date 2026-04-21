# Runbook · Supabase migration rollback

**Last updated** · 2026-04-19
**Owner** · Omkar Jaliparthi
**Related** · [ADR 0001 · Supabase over self-hosted Postgres](../adrs/0001-supabase-over-self-hosted-postgres.md)

---

## Symptom

One or more of:

- A migration was just applied and errors are spiking in the app
- A migration partially applied and the DB is in a mixed schema state
- RLS policy changes broke a previously-working query path

## Severity default

**Critical** if queries against production tables are failing. **High** if a new feature path is broken but existing flows still work.

## Blast radius

- **If the migration touched high-traffic tables** — all users
- **If RLS policies changed** — users in the affected role(s) only
- **If a new table / column was added non-destructively** — usually no regression; the new code path is broken but old code is fine

---

## Immediate checks · first 5 minutes

### 1. What was the last migration applied?

```bash
supabase migration list --linked
```

The newest row with `Applied` is the suspect.

### 2. Is the DB in a valid state?

```sql
-- Connect with psql or Supabase SQL editor
SELECT relname, n_live_tup FROM pg_stat_user_tables
WHERE relname IN (
  -- list tables the migration touched
);
```

Confirm rows exist where expected. If counts look wrong, the migration may have run a destructive step (drop, truncate, rewrite).

### 3. Is RLS the cause?

```sql
SELECT tablename, policyname, cmd, qual
FROM pg_policies
WHERE tablename = '{suspect_table}';
```

Diff against what the migration intended. A policy that reads `auth.uid() = user_id` will silently return zero rows under an anonymous connection — that can look like "data is gone" when the data is fine.

---

## Decision tree

```
Is the migration purely additive (new table, new column, new index)?
├── Yes → roll forward. Do NOT drop the new object — app may have already written to it.
│         Fix the app code that's incompatible. Deploy.
└── No
    │
    Is there a down-migration that is known-safe?
    ├── Yes → apply it. Verify. Continue.
    └── No
        │
        Point-in-time restore (PITR) needed?
        ├── Data loss has occurred → YES. Go to PITR section.
        └── Only schema changed, no data impact → Write a corrective migration.
            Do NOT rely on a hand-rolled rollback unless tested in staging.
```

---

## Recovery

### Option 1 · Roll forward with a corrective migration (preferred)

Safer than a down-migration for anything non-trivial. The corrective migration:

1. Re-creates dropped columns/constraints with compatible defaults
2. Re-enables the RLS policies that broke
3. Is reviewed against the failing migration side-by-side before applying

```bash
supabase migration new rollback_{original_migration_name}
# Edit the file; re-apply what broke, in reverse order of the breaking change
supabase db push --linked
```

### Option 2 · Down-migration (only if tested)

Only use if the down-migration was tested in staging against a representative dataset.

```bash
# There is no first-class down migration command in Supabase CLI.
# You apply the reversing SQL as a new migration.
supabase migration new revert_{N}
# Paste the tested reverse SQL. Apply.
```

### Option 3 · Point-in-time restore (PITR)

**Data-loss incidents only.** Losing minutes of writes is almost always worse than a schema hiccup.

1. Supabase Dashboard → Database → Backups → Point in Time Recovery
2. Choose the timestamp immediately before the bad migration applied
3. **Confirm the target: this creates a new restored branch** — you promote it only after verification
4. Verify row counts and spot-check critical tables in the restored branch
5. Promote or cut over per your runbook for DB failover (this step is significant — do not skip verification)

---

## Rollback of app code

If app code assumes the new schema, roll the app back at the same time as the DB. Schema-then-app mismatches are worse than either alone.

```bash
vercel rollback {PROD_DEPLOYMENT_URL} --yes
```

---

## Postmortem trigger

A postmortem is required if **any** of:

- PITR was invoked
- Any customer data was lost, even briefly
- Exposure window > 15 minutes on a customer-facing path
- Corrective migration required manual DB writes outside the migration system

Template · [postmortems/](../postmortems/)

---

## Hardening backlog

- [ ] Every migration runs against a staging DB seeded from a sanitized prod snapshot, before prod
- [ ] Destructive migrations (`DROP`, `ALTER COLUMN TYPE`, `TRUNCATE`) require an explicit `-- destructive: yes` header comment and a reviewer sign-off
- [ ] Post-migration smoke test — run a fixed query suite and assert expected row counts / shapes
- [ ] Alert on pg error-rate jump in the 15 minutes after a migration applied
