<h1 align="center">TPM × PM Portfolio</h1>

<p align="center">
  <b>Real artifacts from shipped programs — PRDs, RFCs, launch checklists, postmortems, status rollups.</b><br/>
  <i>Curated by <a href="https://github.com/omkarjaliparthi">Omkar Jaliparthi</a> — Product & Program Leader · San Jose</i>
</p>

<p align="center">
  <img src="https://img.shields.io/badge/status-actively_maintained-success?style=for-the-badge" />
  <img src="https://img.shields.io/badge/audience-recruiters_&_hiring_managers-6E56CF?style=for-the-badge" />
  <img src="https://img.shields.io/badge/license-MIT-lightgrey?style=for-the-badge" />
</p>

---

## Why this repo exists

Most PM/TPM resumes tell you the *outcome* — "shipped X," "drove Y% improvement." This repo shows the **artifacts** behind those outcomes: the documents I actually used to run programs, align stakeholders, make decisions, and recover from incidents.

Everything here is either redacted from real programs I've run or built as templates I use today. Where companies/product details are sensitive, names are anonymized — structure and craft are preserved.

---

## 📂 Contents

### 📝 Product & strategy

- **[PRDs/](./prds)** — Product requirement docs with clear problem framing, user segments, success metrics, and scoping decisions
  - [Chargeback Defense Infrastructure (PRD)](./prds/chargeback-defense.md)

### 🏗️ Technical design

- **[RFCs/](./rfcs)** — Technical decision docs for multi-team or high-risk architecture choices
  - [Dual Payment Rails — Stripe + PayPal (RFC)](./rfcs/dual-payment-rails.md)

### 🚨 Incident & postmortem

- **[Postmortems/](./postmortems)** — Blameless incident reviews with timelines, root causes, and corrective actions
  - [Support Agent Escalation Reverting to Tier-1 (2026-04-07)](./postmortems/2026-04-07-support-agent-escalation-persistence.md)

### 🎯 Launch artifacts

- **[Launch Checklists/](./launch-checklists)** — Real launch-readiness checklists from shipped programs
  - [Insights by Omkar — 13-section pre-launch checklist](./launch-checklists/insights-by-omkar.md)

### 📊 Operating rhythm

- **[Status Rollups/](./status-rollups)** — Weekly program status templates for exec + stakeholder audiences
  - [Weekly Status Template (exec-first format)](./status-rollups/weekly-status-template.md)

### *Coming soon*

More artifacts landing as I redact and publish them: pricing & packaging playbooks, RAID log templates, go/no-go frameworks, OKR planning, sprint-cadence templates, changelog discipline guides.

---

## 🔑 Programs these artifacts come from

| Program | Context | Role |
|---|---|---|
| **Insights by Omkar** | Production AI SaaS, solo-shipped in 6 weeks — dual payments, multi-tier support, compliance | Founder / Product / Program / Engineering |
| **Finest Minds portfolio** | $1.2M+, 5 cross-functional teams, 92% on-time delivery | Project Manager (Agile/Scrum) |
| **MediLink** | Healthcare records platform, 10-person team, 6 Agile sprints | Project Manager |
| **Shivshanti Ops** | Franchise operations + incident response, 58% faster MTTR | Business Analyst |

---

## 💡 How I write docs

1. **Start from the customer problem**, not the feature. If I can't write the problem statement in one paragraph, I'm not ready to write the PRD.
2. **Explicit success metrics**, owned and dated. "Improve engagement" is not a metric.
3. **Risks are commitments, not footnotes.** Every RAID entry has an owner, due date, and mitigation.
4. **Decisions get written down.** RFCs, ADRs, decision logs — async > sync for durable alignment.
5. **Postmortems are blameless.** What the system let happen, not who missed what.

---

<p align="center">
  <b>Hiring?</b> <a href="mailto:Jaliparthiomkar03@gmail.com">Jaliparthiomkar03@gmail.com</a> · <a href="https://github.com/omkarjaliparthi">GitHub</a> · <a href="https://www.linkedin.com/in/jaliparthiomkar">LinkedIn</a>
</p>
