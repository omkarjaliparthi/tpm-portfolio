# ADR 0004 · Release cadence philosophy

**Status** · Accepted · 2026-03-01
**Author** · Omkar Jaliparthi
**Program** · Insights by Omkar · Insights Astrology API

---

## Context

The portfolio ships often — sometimes many releases in a day during an iteration burst. That pattern is either a strength signal (fast feedback, small changes, low risk per release) or an anti-pattern (tag spam, missing test discipline). It matters which one is true, and the answer needs to be written down so decisions stay consistent.

## Options

### A · Weekly release train · fixed cadence

- Predictable for customers
- Slows down small, safe fixes unnecessarily
- Encourages batch-size bloat near the cutoff

### B · Continuous deployment · every merged PR ships

- Smallest change per release · lowest risk per release
- Requires strong automated test coverage and observability
- Changelog can become noisy without discipline

### C · Semver-gated bursts · ship as small and often as safe, discipline at version boundaries

- Patch releases ship freely; minor and major require changelog review
- Breaking changes are batched and announced
- Works for a solo team; scales to more

## Decision

**Option C.** Patch versions ship whenever a change is green; minor versions group customer-visible changes with a written changelog entry; major versions are rare and pre-announced.

## Rules

1. **Every release has a tag.** No "latest" deploys without a version.
2. **Changelog is the source of truth.** If a change isn't worth writing one line about, it isn't worth a minor bump.
3. **Patch releases are cheap.** Fix-and-ship beats batch-and-wait. Burst days are expected.
4. **Breaking changes require a notice window.** Minimum two minor versions of deprecation before removal on the API.
5. **Release burst ≠ signal of quality.** 30 tagged releases in a day means iteration speed. Quality is measured by defect rate per release, not release rate.

## Consequences

**Accepted:**
- Someone skimming the release page may see 60 entries in a day and assume chaos
- Automated release tooling is non-negotiable — manual cuts don't survive this cadence

**Won:**
- Smallest possible rollback surface per incident
- Fixes reach users in minutes, not days
- Semver discipline stays honest because patch and minor are genuinely different things

## Counter-signals · when to slow down

- Defect rate per release trending up → batch more, gate harder
- Changelog entries trending toward "misc" or "tweak" → batch, don't ship
- On-call fatigue → cadence is a person problem now, slow down

## Related

- [Metrics methodology](../methodology.md) — defect-rate-per-release definition
- [Postmortem · 2026-04-07 · Support Agent Escalation](../postmortems/2026-04-07-support-agent-escalation-persistence.md) — fast cadence enabled same-day fix
