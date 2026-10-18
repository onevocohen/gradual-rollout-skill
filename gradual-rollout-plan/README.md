# Gradual Rollout Plan Skill

A Cursor Agent Skill that guides a Product Manager through building a structured feature release plan, based on the **Idira Gradual Release Framework**.

---

## What it does

Given a PRD or feature description, the skill:

1. Reads the PRD and infers NFI status, impact, rollback complexity, region and service constraints, and dates — then presents its conclusions for a quick PM confirm rather than asking questions
2. Classifies the release into one of three paths — **Lightweight**, **Standard**, or **High-Touch**
3. Maps the mandatory release rings (R0–R4) with clear R&D vs. PM ownership
4. Builds a tailored **R3 Production Canary cohort plan** with exposure waves, targeting criteria, and go/no-go gates
5. Outputs a ready-to-share release plan document

---

## Release paths at a glance

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

Test Tenants always go first. Live tenants are sequenced by ARR:

```
Wave 0  →  Test Tenants
Wave 1  →   5%  (lowest ARR)
Wave 2  →  15%
Wave 3  →  35%
Wave 4  →  65%
Wave 5  → 100%  (all eligible)
```

Waves can be adjusted for service constraints, region restrictions, and timeline pressure.

---

## How to trigger

In any Cursor chat, just say:

> *"Build a gradual rollout plan for [feature]"*

or paste a PRD and ask for a rollout plan. You can also invoke it explicitly:

> `/gradual-rollout-plan`

---

## Files

| File | Purpose |
|------|---------|
| `SKILL.md` | Main skill — intake, classification logic, output template |
| `reference.md` | Detailed rules, LaunchDarkly checklist, gate criteria, industry benchmarks |
| `README.md` | This file |

---

## Source

Based on the *Idira Gradual Release Framework* deck — Product Ops, October 2026.
