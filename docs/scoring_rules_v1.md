# Scoring Rules v1 — Deterministic Evaluation Policy

## Purpose

This document defines the deterministic scoring policy used to evaluate a candidate
against a job’s normalized backend requirements.

The purpose of scoring is to:
- summarize alignment and hiring risk in a repeatable way
- enable consistent comparison across different job descriptions
- preserve full auditability of every evaluation decision

This policy does **not**:
- predict job performance
- optimize resumes or keywords
- replace human judgment
- infer or assume unproven experience

All outcomes must be explainable line-by-line using frozen inputs.

---

## Inputs

The evaluator operates exclusively on frozen artifacts:

- **Job requirements**
  - Normalized requirement codes
  - Each classified as MUST or NICE

- **Evidence mappings**
  - One status per requirement:
    SUPPORTED, PARTIAL, UNSUPPORTED, or ABSTAIN
  - Each mapping includes explicit rationale and confidence

- **Candidate evidence inventory**
  - Referenced by ID only
  - No new evidence may be introduced during scoring

The evaluator must not reinterpret requirements, infer missing experience, or
upgrade evidence strength.

---

## Requirement Status Definitions

### SUPPORTED
Meaning:
- There is explicit, provable evidence that satisfies the requirement.

Not allowed:
- Inferring tooling, depth, or scale that is not stated.
- Treating adjacent skills as substitutes.

---

### PARTIAL
Meaning:
- Related evidence exists, but required depth, scope, or ownership is missing or unclear.

Examples:
- Coursework instead of applied usage
- Small-scale exposure where operational depth is expected

Not allowed:
- Upgrading to SUPPORTED based on learning potential or assumptions.

---

### UNSUPPORTED
Meaning:
- No evidence supports the requirement.

Not allowed:
- Filling gaps using intent, proximity, or role adjacency.

---

### ABSTAIN
Meaning:
- A deliberate refusal to claim support due to ambiguity in the job description
  or insufficiency of evidence.

ABSTAIN exists to preserve correctness when:
- requirements are underspecified
- evidence relevance is unclear

Not allowed:
- Using ABSTAIN to avoid penalties
- Treating ABSTAIN as partial credit

For scoring purposes, ABSTAIN is treated as not satisfied.

---

## MUST vs NICE Semantics

MUST requirements represent screening gates and dominant risk drivers.
Failure to meet a MUST materially increases hiring risk.

NICE requirements represent secondary signals and tie-breakers.
They cannot compensate for missing MUST requirements.

Therefore:
- MUST requirements carry higher weight
- NICE requirements have capped contribution
- Missing MUSTs trigger explicit penalties

This reflects real hiring behavior, especially for entry-level and junior roles.

---

## Scoring Table

Points awarded per requirement:

| Requirement Type | SUPPORTED | PARTIAL | UNSUPPORTED | ABSTAIN |
|------------------|-----------|---------|-------------|---------|
| MUST             | 1.00      | 0.50    | 0.00        | 0.00    |
| NICE             | 0.50      | 0.25    | 0.00        | 0.00    |

Rationale:
- PARTIAL represents incomplete fulfillment and retains hiring risk.
- NICE is intentionally capped to prevent padding.
- ABSTAIN is visible but does not improve scores.

---

## Penalties

### Must-Gap Penalty

Trigger:
- At least one MUST requirement is UNSUPPORTED or ABSTAIN.

Effect:
- Multiply the total score by **0.85**.

Rationale:
- Hiring risk increases non-linearly when a MUST is missing.
- This prevents NICE accumulation from masking core gaps.

The evaluator must explicitly report:
- whether the penalty triggered
- which requirements caused it

---

## Final Labels

Final labels are derived from both MUST coverage and overall score.

### STRONG_MATCH
- must_coverage ≥ 0.85
- overall_score ≥ 0.75
- zero MUST requirements are UNSUPPORTED

Meaning:
- Candidate satisfies core expectations with limited risk.

---

### BORDERLINE
- must_coverage ≥ 0.65
- overall_score ≥ 0.55

Meaning:
- Candidate shows meaningful alignment but carries notable gaps.
- Requires targeted interview probing.

---

### WEAK_MATCH
- Any case not meeting the above criteria.

Meaning:
- Candidate does not meet core expectations for this role definition.

---

## Role-Critical Requirements (Acknowledged, Not Enforced)

Some requirements may be **role-critical**, meaning they define the identity of the role
(e.g. fundamental backend concepts such as API usage).

At this stage:
- Role-critical requirements are **not enforced as hard gates**
- They do **not override scoring outcomes**
- They do **not automatically reject candidates**

Instead, the system may surface missing role-critical requirements as
**explicit risk signals** intended for human review.

This preserves:
- determinism
- transparency
- human-in-the-loop decision making

Future versions may formalize enforcement once sufficient empirical grounding exists.

---

## Non-Goals

This system is explicitly **not**:
- an ATS keyword optimizer
- a resume writing tool
- an AI ranking system
- a performance predictor
- a substitute for interviews

It is not allowed to:
- assume unproven experience 
- inflate evidence strength
- soften unsupported requirements for coverage

Correctness and honesty take precedence over match optimization.

---

## Versioning

This document is versioned.

Any change requires:
- incrementing the version identifier
- updating golden test expectations
- recording the rationale for the policy change

Policy drift without traceability is not permitted.
