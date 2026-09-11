# Design Before Code — Roadmap

This roadmap follows `docs/PROJECT-CHARTER.md`: the project exists to make consequential business, UX, and data-model decisions reviewable before implementation.

Method evolution must also follow [`docs/METHOD-SYNTHESIS.md`](METHOD-SYNTHESIS.md): core Skills should synthesize proven upstream mechanisms rather than evolve as isolated prompts or copied templates.

## Current state

The first four-skill loop has synthesized and benchmarked minimum-usable versions:

- `business-domain-design` — v0.2.2
- `ux-flow-design` — v0.2.1
- `data-model-design` — v0.2.3
- `design-readiness-review` — v0.2

Infrastructure:

- Project Charter / anti-drift rules
- Method Synthesis / upstream influence map
- low-cost Evaluation Protocol
- per-skill focused regression evals
- manufacturing example
- OpenSpec custom schema bundle under `integrations/openspec/design-before-code/`
- recorded OpenSpec validation evidence in `integrations/openspec/design-before-code/VALIDATION.md`
- first Level 3 realistic benchmark evidence in `evals/level3-training-reimbursement.md`

## Core skill set

### 1. `business-domain-design`

Answers: **What business world are we actually building?**

Synthesizes collaborative design gating, complexity-sensitive planning, and selected DDD discovery techniques. It chooses Plain CRUD / Light Domain Modeling / Deep Domain Discovery before using ceremony, and uses Event Storming-lite / ubiquitous language only where they clarify real complexity.

### 2. `ux-flow-design`

Answers: **How does a human accomplish the business task naturally?**

Focuses on the behavioral experience contract: named-protagonist journeys, task-first information architecture, surface responsibilities, state/recovery coverage, low-fidelity wireframes, accessibility floor, and surface closure. Visual-brand/design-system work remains separate.

### 3. `data-model-design`

Answers: **What data exists, why does it exist, and will history still mean the right thing later?**

Synthesizes explicit data-model artifacts with normalization, constraints, access-pattern-driven physical design, temporal semantics, scenario validation, and human decision locks. Its positive gate means ready for downstream technical design, not implementation approval.

### 4. `design-readiness-review`

Answers: **Do the business model, user flow, and data model describe the same system?**

Synthesizes readiness/cohesion review, staged gates, and explicit human approval. It runs coverage lenses, reconciles decisions, traces primary tasks across Business → UX → Data, re-checks project constraints after design, and routes defects back to the owning Skill.

## Development phases

### Phase A — Complete and synthesize the design triangle

**Complete at minimum-usable level.**

- business-domain-design established
- ux-flow-design established
- data-model-design established
- upstream method synthesis documented

### Phase B — Cross-artifact review

**Complete at minimum-usable level.**

- design-readiness-review v0.2 exists
- compact contradiction evals exist
- synthesis smoke test passed 4/4
- readiness result is explicitly separate from human approval

### Phase C — OpenSpec orchestration

**Complete at the current experimental level.**

Current bundle:

```text
integrations/openspec/design-before-code/
├── schema.yaml
├── README.md
├── INTEGRATION.md
├── VALIDATION.md
└── templates/
```

Implemented flow:

```text
proposal
  ↓
business-model
  ↓
ux-flow
  ↓
data-model
  ↓
design-readiness
  ↓
human-approval
  ↓
specs
  ↓
technical-design
  ↓
tasks
  ↓
apply
```

Validated locally with OpenSpec 1.8.0 on 2026-09-10:

- project schema discovery succeeded;
- `openspec schema validate design-before-code --verbose` passed YAML, structure, template, and dependency-graph checks;
- `openspec schema which design-before-code` resolved the project-local bundle;
- `openspec new change dbc-smoke --schema design-before-code` succeeded;
- runtime execution delegated to all four Design Before Code Skills;
- the agent stopped at `human-approval` without creating or modifying the approval artifact;
- `APPROVAL: PENDING` kept the semantic gate closed even after OpenSpec structurally unlocked specs;
- human-authored `APPROVAL: APPROVED` unlocked specs → technical-design → tasks;
- final planning status reached 9/9 artifacts complete without running apply or writing application code.

Design choices retained:

- keep each core Skill independently usable;
- precheck companion Skill availability instead of silently falling back;
- retain OpenSpec delta specs, technical design, task tracking, apply/verify/archive after approval;
- treat `human-approval.md` as a human-only operational contract;
- re-check readiness/approval in specs, tasks, and apply instructions;
- acknowledge that OpenSpec `requires` edges are artifact-availability relationships, not actor-authenticated business gates;
- leave identity-level enforcement to optional external CI/hook/review integration.

Phase C should remain stable unless a later realistic benchmark reveals a general orchestration or gate defect.

### Phase D — End-to-end milestone validation

**Complete at the current experimental level.**

The first Level 3 Domain Benchmark used **Employee Training Request + Training Expense Reimbursement** and reached:

```text
READINESS: READY_FOR_HUMAN_APPROVAL
```

The run was intentionally bounded:

- one realistic greenfield domain;
- mixed-agent continuation to exercise interoperability;
- no no-skill baseline;
- no A/B loop;
- no memory/session clearing;
- no apply/code;
- no repeat of the Product Category smoke case.

The benchmark exercised lifecycle, exception, permission, historical decision, effective-dated policy, calculation, payment outcome, and human-decision gates.

General findings were absorbed as compact v0.2.x correctness improvements:

- `business-domain-design v0.2.2` — reconcile a human-resolved decision through every affected active section before readiness;
- `ux-flow-design v0.2.1` — unresolved means uninstantiated; validate exception/recovery reachability with lifecycle/time/permission gate algebra;
- `data-model-design v0.2.3` — strengthen dual-path identity, stored-derived-value, and reference+snapshot integrity review;
- `design-readiness-review v0.2` — no version bump; it correctly found reconciliation and reachability defects, withheld readiness, routed owners, and later returned READY after correction.

Detailed evidence and defect classification are recorded in `evals/level3-training-reimbursement.md`.

Do **not** immediately rerun this benchmark after the compact Skill fixes. The fixes are covered by focused regression definitions instead.

### Phase E — Packaging, release hardening, and broader confidence

**Current phase.**

Near-term priorities:

- improve installation and use documentation;
- make Skill distribution / project-local installation straightforward across supported agents;
- document a small end-to-end example without turning it into a new paid benchmark;
- clarify experimental/alpha stability boundaries;
- verify version references and packaging consistency;
- prepare the repository for an early alpha release.

Only after packaging/release hardening is stable should broader confidence work expand:

- decide whether optional CI/hook approval enforcement is worth adding;
- add milestone cross-domain benchmarks when a genuinely different domain is needed;
- refine Skill interoperability and distribution from observed agent differences;
- periodically review mature upstream methods for transferable improvements;
- consider early-MVP review mode;
- consider a separate brownfield/reverse-engineering project or skill family if justified.

Do not claim production stability from one Level 3 benchmark.

## Near-term maintenance policy

After Phase D:

- `business-domain-design v0.2.x`: compact correctness fixes only.
- `ux-flow-design v0.2.x`: compact correctness fixes only.
- `data-model-design v0.2.x`: correctness and domain-general regression fixes only.
- `design-readiness-review v0.2.x`: cross-artifact correctness and routing fixes only when a verified defect exists.
- OpenSpec schema: fix only verified orchestration, compatibility, or gate failures; do not duplicate Skill logic into schema instructions.
- Prefer focused regression definitions over another Level 3 run for ordinary v0.2.x fixes.

## What we should not do next

- Keep polishing any one Skill until it becomes a giant prompt.
- Copy upstream SKILL.md files or templates into this repository and rename them.
- Re-invent mature planning/domain/UX/schema mechanisms without first checking established approaches.
- Import DDD, visual-design-system, or architecture ceremony without a user-problem reason.
- Add Figma/design-system generation before task-flow reasoning has been validated end to end.
- Build a full legacy/brownfield reverse-engineering framework.
- Require repeated no-skill A/B tests for routine edits.
- Let `design-readiness-review` silently fix product decisions rather than route them upstream.
- Let OpenSpec become the product identity; it remains the orchestration layer.
- Claim schema dependencies alone provide authenticated human approval.
- Re-run the Product Category smoke case as a benchmark; it has already served its orchestration purpose.
- Re-run the Training Reimbursement Level 3 benchmark merely because compact v0.2.x fixes were made.

## Definition of the first meaningful milestone

The first meaningful milestone is reached when a user can provide rough meeting notes and obtain, before coding:

1. a business/domain model they can understand;
2. a page/task flow they can challenge;
3. a data model they can review;
4. a cross-artifact readiness report;
5. an explicit human approval gate;
6. orchestration that preserves those gates before tasks/apply;
7. one end-to-end realistic greenfield validation.

**This milestone has now been reached at an experimental level.**

The next objective is not another benchmark loop; it is packaging and release hardening toward an early alpha while preserving the project's low-cost evaluation discipline.