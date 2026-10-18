---
name: gradual-rollout-plan
description: Guides a Product Manager through building a structured gradual release plan based on the Idira Gradual Release Framework. Use when a PM asks to create a rollout plan, release plan, gradual rollout, canary plan, or release strategy for a feature. Also use when the PM mentions NFI, release rings, LaunchDarkly flags, or customer exposure waves.
---

# Gradual Rollout Plan

Generates a structured release plan following the Idira Gradual Release Framework: classify the release path, identify mandatory rings, and produce a tailored R3 cohort plan.

## Step 1 — Read and Infer from the PRD

Read the PRD in full. Infer every signal below directly from the content — do not ask the PM for information you can determine yourself.

| Signal | What to look for in the PRD |
|--------|-----------------------------|
| **NFI** | Explicit "NFI" label, "new feature", "not previously available", or net-new capability with no prior version |
| **Impact** | Scope of affected users/tenants, criticality language ("critical path", "core workflow"), data sensitivity, potential for data loss or service disruption |
| **Rollback complexity** | Mentions of schema migrations, data writes, third-party dependencies, customer comms required, or irreversible state changes → Coordinated or Irreversible. Flag flip only → Easy |
| **Region constraints** | Regulatory mentions (GDPR, FedRAMP, data residency), geography-specific requirements, or region-scoped rollout language |
| **Service constraints** | Named services, integrations, or components the feature depends on or affects |
| **Earliest start** | Sprint dates, milestone dates, or "ready for QA" signals in the PRD |
| **Target GA** | Committed launch date, OKR deadline, or release milestone |

After reading, present your inferences to the PM in this format — **do not ask open questions**:

```
Based on the PRD, here is how I've classified this release:

- NFI: [Yes / No / Unclear — reason]
- Impact: [High / Medium / Low — reason]
- Rollback: [Easy / Coordinated / Irreversible — reason]
- Region constraints: [None / list — reason]
- Service constraints: [None / list — reason]
- Earliest start: [date or "not found"]
- Target GA: [date or "not found"]

→ Release path: [LIGHTWEIGHT / STANDARD / HIGH-TOUCH]

Please confirm or correct any of the above before I generate the plan.
```

Only ask an explicit question if a signal is genuinely absent from the PRD (e.g. no dates mentioned at all).

---

## Step 2 — Classify the Release Path

Apply this decision tree in order:

```
NFI = Yes  →  HIGH-TOUCH
OR  Impact = High
OR  Rollback = Irreversible

Impact = Medium  →  STANDARD
OR  Rollback = Coordinated

All others  →  LIGHTWEIGHT
```

---

## Step 3 — Map Required Rings

| Path | Mandatory rings | R&D owns | PM owns |
|------|----------------|----------|---------|
| **LIGHTWEIGHT** | R0 → R4 | R0 | R4 |
| **STANDARD** | R0 → R3 → R4 | R0 | R3, R4 |
| **HIGH-TOUCH** | R0 → R1 → R2 → R3 → R4 | R0, R1 | R2, R3, R4 |

Ring definitions:
- **R0** Internal QA — engineering/QA environments
- **R1** Customer Zero / Dogfooding — internal teams
- **R2** Customer One — selected Early Access customers (via NFI process)
- **R3** Production Canary — Test Tenants → live cohorts via LaunchDarkly flags
- **R4** General Availability — all eligible production tenants

---

## Step 4 — Build the R3 Cohort Plan (Standard & High-Touch only)

R3 always starts with **Test Tenants** (customer-purchased sandboxes). Then sequence live tenants:

**Default exposure wave template:**

| Wave | Cumulative % of eligible live tenants | Targeting criteria |
|------|---------------------------------------|--------------------|
| 1 | 5% | Lowest ARR, small tenant count |
| 2 | 15% | Low-to-mid ARR |
| 3 | 35% | Mid ARR |
| 4 | 65% | Mid-to-high ARR |
| 5 | 100% | All remaining eligible tenants |

Apply these adjustments based on PM inputs:
- **Service constraints** → add service-filter targeting to each wave
- **Region constraints** → add region filter to early waves, remove for later waves
- **Short timeline** → consider collapsing waves (e.g. 5% → 35% → 100%)
- **High ARR sensitivity** → add high-ARR tenants to wave 4 only, with extended bake time

For each wave, include: target %, cohort description, gate criteria (metrics + bake time), owner.

---

## Step 5 — Output the Release Plan

Use this template exactly:

```markdown
# Gradual Release Plan — [Feature Name]

**Release path:** [LIGHTWEIGHT / STANDARD / HIGH-TOUCH]
**Earliest start:** [date]  |  **Target GA:** [date]

---

## Required Rings

| Ring | Stage | Owner | Entry gate | Exit gate |
|------|-------|-------|------------|-----------|
| R0 | Internal QA | R&D | Merge to main | QA sign-off |
| [include only rings in the mandatory path] |
| R4 | General Availability | PM | R3 100% wave complete | PM go/no-go |

---

## R3 Cohort Plan  _(Standard & High-Touch only)_

**First:** Test Tenants — confirm safe activation before live tenants.

| Wave | % | Who | Targeting | Gate |
|------|---|-----|-----------|------|
| 0 | — | Test Tenants | External test tenants | No incidents 48 h |
| 1 | 5% | Low ARR | [criteria] | [metric] for [bake time] |
| 2 | 15% | Low–mid ARR | [criteria] | [metric] for [bake time] |
| 3 | 35% | Mid ARR | [criteria] | [metric] for [bake time] |
| 4 | 65% | Mid–high ARR | [criteria] | [metric] for [bake time] |
| 5 | 100% | All eligible | Remove filters | [metric] for [bake time] |

---

## Owners & Responsibilities

- **R&D** — Implements LaunchDarkly flags, owns internal rings (R0–R1)
- **PM** — Defines cohort plan, owns customer exposure (R2–R4), signs go/no-go gates
- **Product Ops** — Reviews plan against mandatory guidelines

## Open Questions / Assumptions

- [List any facts assumed or still to be confirmed]
```

---

## Reference

For the full framework rationale, industry examples, and LaunchDarkly validation checklist, see [reference.md](reference.md).
