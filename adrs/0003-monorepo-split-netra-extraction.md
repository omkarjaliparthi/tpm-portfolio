# ADR 0003 · Monorepo split · Netra extraction

**Status** · Accepted · 2026-04-14
**Author** · Omkar Jaliparthi
**Program** · Insights Astrology API / Netra

---

## Context

The astronomy API repo had grown to hold three things in one deployable:

1. The engine and public HTTP API — the *product* sold to developers
2. A browser-native studio that consumes the API — the *flagship experience* for end users
3. Operator tooling — auth issuance, admin views, internal clients

Three concerns, one release cadence, one change surface. The studio's UX iteration speed was constrained by the API's semver discipline, and the API's release notes were polluted by frontend churn.

## Options

### A · Keep everything in one repo, use feature flags

- Lowest short-term cost
- Perpetuates the coupling; doesn't solve release-cadence mismatch
- Every studio UX change keeps appearing in API changelogs

### B · Monorepo with workspaces (npm / pnpm)

- One repo, isolated packages, shared tooling
- Good when teams share code heavily
- For a one-person team with three deploys, still couples releases because repo-level tags are shared

### C · Full repo split — extract studio to its own repo (Netra)

- API repo becomes the product spec
- Studio repo owns its own releases and can iterate without touching API versioning
- Two CI pipelines, two dependency update streams, cross-repo coordination for breaking API changes

## Decision

**Option C.** Extracted the studio into a dedicated repo (`insights-by-omkar-netra`) on 2026-04-14.

## Consequences

**Accepted:**
- Breaking API changes now require coordinated releases — studio bumps its dependency after API ships
- Shared UI primitives duplicated initially; factored out later only if a third consumer appears
- Two `CHANGELOG.md` files to maintain

**Won:**
- API release notes are clean and developer-facing — no "tweaked spacing" entries
- Studio ships UX-only changes multiple times a day without touching API semver
- Easier to reason about what is *sold* versus what is *shown*
- Admin tooling was extracted the same way into `insights-commandcenter`

## Exit criteria

Revisit if any hold:

- Coordinated changes become the common case rather than the exception
- Shared code duplication exceeds ~20% of either repo
- A contributor joins and the split makes onboarding meaningfully harder than it solves

## Related

- [ADR 0004 · Release cadence philosophy](./0004-release-cadence-philosophy.md)
- [Portfolio strategy](../portfolio-strategy.md)
