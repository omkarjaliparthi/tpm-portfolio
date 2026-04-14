# Postmortems

Blameless incident reviews. Every production issue that affects users or breaks invariants becomes a postmortem candidate. These are the artifacts that come out.

## Contents

- **[2026-04-07 — Support Agent Escalation Reverting to Tier-1](./2026-04-07-support-agent-escalation-persistence.md)** — `chat_messages` insert path used a stale default, overwriting escalation state on subsequent messages. Fixed same day; led to smoke-test improvements and ops dashboard planning.

---

*Postmortem format: Summary → Impact → Timeline → Root cause → Contributing factors → What went well/badly → Corrective actions → Lessons. Adapted from Google SRE's blameless template.*
