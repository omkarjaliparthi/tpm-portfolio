# Runbooks

On-call triage and recovery procedures. Each runbook answers one question: *"I just got paged. What do I do in the next 5 minutes?"*

## Format

Every runbook opens with:

- **Symptom** — what the page looks like to the on-call
- **Severity default** — starting assumption; downgrade or escalate based on findings
- **Blast radius** — who is affected, in what way

Then:

- **Immediate checks** — the first 3 things to look at, in order
- **Decision tree** — what to do for each finding
- **Rollback / recovery** — how to stop the bleeding before root-cause analysis
- **Postmortem trigger** — when this incident graduates into a written postmortem

## Index

- [Stripe webhook triage](./stripe-webhook-triage.md)
- [Supabase migration rollback](./supabase-migration-rollback.md)

## Writing rules

1. **No prose where a checklist works.** On-call at 2am doesn't read prose.
2. **Every command is copy-paste runnable.** Placeholders in `{braces}`, not vague English.
3. **Every step has an expected outcome.** "If you don't see X, jump to section Y."
4. **The runbook must survive the author leaving.** No tribal knowledge.
