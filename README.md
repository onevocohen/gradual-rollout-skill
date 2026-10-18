# Gradual Rollout Plan — Cursor Agent Skill

A Cursor Agent Skill that guides a Product Manager through building a structured feature release plan, based on the **Idira Gradual Release Framework** (Product Ops, October 2026).

---

## What it does

Given a PRD or feature description, the skill:

1. Reads the PRD and asks only for what's missing across 8 intake questions
2. Classifies the release into one of three paths — **Lightweight**, **Standard**, or **High-Touch**
3. Maps the mandatory release rings (R0–R4) with clear R&D vs. PM ownership
4. Builds a tailored **R3 Production Canary cohort plan** with exposure waves, targeting criteria, and go/no-go gates
5. Outputs a ready-to-share release plan document

---

## Release paths

| Path | Triggers | Rings |
|------|----------|-------|
| **Lightweight** | Low impact + easy rollback + not NFI | R0 → R4 |
| **Standard** | Medium impact or coordinated rollback | R0 → R3 → R4 |
| **High-Touch** | NFI, high impact, or irreversible rollback | R0 → R1 → R2 → R3 → R4 |

---

## Release rings

| Ring | Stage | Owner |
|------|-------|-------|
| R0 | Internal QA | R&D |
| R1 | Customer Zero / Dogfooding | R&D |
| R2 | Customer One / Early Access | PM |
| R3 | Production Canary (Test Tenants → live cohorts) | PM |
| R4 | General Availability | PM |

---

## R3 default exposure waves

Test Tenants always go first. Live tenants are then sequenced by ARR:

| Wave | Cumulative % of eligible live tenants | Targeting |
|------|---------------------------------------|-----------|
| 0 | — | Test Tenants |
| 1 | 5% | Lowest ARR |
| 2 | 15% | Low–mid ARR |
| 3 | 35% | Mid ARR |
| 4 | 65% | Mid–high ARR |
| 5 | 100% | All eligible |

Waves can be adjusted for service constraints, region restrictions, and timeline pressure.

---

## How to trigger

In any Cursor chat, say:

> *"Build a gradual rollout plan for [feature]"*

or paste a PRD and ask for a rollout plan. You can also invoke it explicitly:

> `/gradual-rollout-plan`

---

## Skill files

| File | Purpose |
|------|---------|
| [`gradual-rollout-plan/SKILL.md`](gradual-rollout-plan/SKILL.md) | Main skill — intake questions, classification logic, output template |
| [`gradual-rollout-plan/reference.md`](gradual-rollout-plan/reference.md) | Detailed rules, LaunchDarkly checklist, gate criteria, industry benchmarks |
| [`gradual-rollout-plan/README.md`](gradual-rollout-plan/README.md) | Skill-level readme |

---

## Source

Based on the *Idira Gradual Release Framework* — Product Ops, October 2026.
