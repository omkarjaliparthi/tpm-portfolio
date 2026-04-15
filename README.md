<h1 align="center">TPM × PM Portfolio</h1>

<p align="center">
  <b>Artifacts from shipped programs — PRDs, RFCs, launch checklists, postmortems, status rollups.</b><br/>
  <i>By <a href="https://github.com/omkarjaliparthi">Omkar Jaliparthi</a> · Product & Program Leader · San Jose</i>
</p>

<p align="center">
  <img src="https://img.shields.io/badge/status-actively_maintained-success?style=for-the-badge" />
  <img src="https://img.shields.io/badge/audience-recruiters_&_hiring_managers-6E56CF?style=for-the-badge" />
  <img src="https://img.shields.io/badge/license-MIT-lightgrey?style=for-the-badge" />
</p>

---

## Why this exists

Most PM/TPM resumes report outcomes — "shipped X," "drove Y%." This repo shows the **artifacts behind those outcomes** — documents I actually used to run programs, align stakeholders, make decisions, and recover from incidents.

Everything here is either redacted from real programs or a template I use today. Where details are sensitive, names are anonymized. Structure and craft preserved.

---

## 📂 Contents

### 📝 Product & strategy

- **[PRDs/](./prds)** — problem framing, user segments, success metrics, scoping
  - [Chargeback Defense Infrastructure](./prds/chargeback-defense.md)
  - [Insights Astrology API · Public Launch](./prds/astrology-api-launch.md)

### 🏗️ Technical design

- **[RFCs/](./rfcs)** — decision docs for multi-team or high-risk architecture
  - [Dual Payment Rails · Stripe + PayPal](./rfcs/dual-payment-rails.md)
  - [Home-Grown Ephemeris Engine · Build vs Buy](./rfcs/home-grown-ephemeris-engine.md)

### 🚨 Incident & postmortem

- **[Postmortems/](./postmortems)** — blameless reviews · timelines · root causes · corrective actions
  - [Support Agent Escalation Reverting to Tier-1 · 2026-04-07](./postmortems/2026-04-07-support-agent-escalation-persistence.md)

### 🎯 Launch artifacts

- **[Launch Checklists/](./launch-checklists)** — readiness checklists from shipped programs
  - [Insights by Omkar · 13-section pre-launch checklist](./launch-checklists/insights-by-omkar.md)

### 📊 Operating rhythm

- **[Status Rollups/](./status-rollups)** — weekly program status templates
  - [Weekly Status · exec-first format](./status-rollups/weekly-status-template.md)

### *Coming soon*

Pricing & packaging playbooks · RAID log templates · go/no-go frameworks · OKR planning · sprint cadence · changelog discipline.

---

## 🔑 Programs these artifacts come from

| Program | Context | Role |
|---|---|---|
| **Insights by Omkar** | Production AI SaaS, solo-shipped in 6 weeks — dual payments, multi-tier support, compliance | Founder / Product / Program / Engineering |
| **Insights Astrology API** | Commercial astronomy API — 43 endpoints, first-principles VSOP87D + ELP2000 engine, TS/Python/Go SDKs | Founder / Product / Architecture |
| **Finest Minds portfolio** | $1.2M+, 5 cross-functional teams, 92% on-time delivery | Project Manager (Agile/Scrum) |
| **MediLink** | Healthcare records platform, 10-person team, 6 Agile sprints | Project Manager |
| **Shivshanti Ops** | Franchise operations + incident response, 58% faster MTTR | Business Analyst |

---

## 💡 How I write docs

1. **Start from the customer problem**, not the feature. If I can't state the problem in one paragraph, I'm not ready to write the PRD.
2. **Explicit success metrics** — owned and dated. "Improve engagement" is not a metric.
3. **Risks are commitments, not footnotes.** Every RAID entry has an owner, due date, and mitigation.
4. **Decisions get written down.** RFCs, ADRs, decision logs. Async > sync for durable alignment.
5. **Postmortems are blameless.** What the system let happen, not who missed what.

---

<p align="center">
  <b>Hiring?</b> <a href="mailto:Jaliparthiomkar03@gmail.com">Jaliparthiomkar03@gmail.com</a> · <a href="https://github.com/omkarjaliparthi">GitHub</a> · <a href="https://www.linkedin.com/in/jaliparthiomkar">LinkedIn</a>
</p>
