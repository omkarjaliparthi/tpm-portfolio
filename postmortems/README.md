# Postmortems

Blameless incident reviews. Every production issue that affects users or breaks invariants becomes a postmortem candidate. These are the artifacts that come out.

## Contents

- **[2026-04-07 · Support Agent Escalation Reverting to Tier-1](./2026-04-07-support-agent-escalation-persistence.md)** — `chat_messages` insert path used a stale default, overwriting escalation state on subsequent messages. Fixed same day; led to smoke-test improvements and ops dashboard planning.
- **[2026-04-02 · RLS policy drift caused empty admin panel](./2026-04-02-rls-policy-drift-admin-panel.md)** — migration renamed a role column; admin policy narrowed silently. Ops-facing only; surfaced "sanitized prod snapshot" as the right staging-data strategy.
- **[2026-03-30 · Credit refund double-credit under idempotency-window edge](./2026-03-30-credit-refund-idempotency-window.md)** — idempotency key scoped to the request, not the underlying failed generation. Found in self-audit before customer impact; shipped DB-level uniqueness on compensating entries.
- **[2026-03-18 · API rate limiter silently fell back to in-memory](./2026-03-18-api-rate-limiter-fallback.md)** — one-way fallback to a per-instance counter after a transient upstream error. Led to bidirectional fallback pattern and alerting on fallback state.

---

*Postmortem format · Summary → Impact → Timeline → Root cause → Contributing factors → What went well/badly → Corrective actions → Lessons. Adapted from Google SRE's blameless template.*
