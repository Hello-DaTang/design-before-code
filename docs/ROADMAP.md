# Design Before Code — Roadmap

This roadmap follows `docs/PROJECT-CHARTER.md`: the project exists to make consequential business, UX, and data-model decisions reviewable before implementation.

Method evolution must also follow [`docs/METHOD-SYNTHESIS.md`](METHOD-SYNTHESIS.md): core Skills should synthesize proven upstream mechanisms rather than evolve as isolated prompts or copied templates.

## Current state

The first four-skill loop has synthesized minimum-usable versions:

- `business-domain-design` — v0.2
- `ux-flow-design` — v0.2
- `data-model-design` — v0.2.2
- `design-readiness-review` — v0.2

Infrastructure:

- Project Charter / anti-drift rules
- Method Synthesis / upstream influence map
- low-cost Evaluation Protocol
- per-skill regression evals
- manufacturing example
- OpenSpec custom schema bundle under `integrations/openspec/design-before-code/`
- recorded OpenSpec validation evidence in `integrations/openspec/design-before-code/VALIDATION.md`

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

- business-domain-design v0.2
- ux-flow-design v0.2
- data-model-design v0.2.2
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

Phase C should now remain stable unless a later realistic benchmark reveals a general orchestration or gate defect.

### Phase D — End-to-end milestone validation

**Current phase.**

Run one realistic, moderately complex greenfield requirement through:

```text
requirements
→ proposal
→ business-model
→ ux-flow
→ data-model
→ design-readiness
→ human decisions / approval
→ specs
→ technical-design
→ tasks
```

This is a Level 3 Domain Benchmark, not a routine development loop.

The benchmark should be meaningfully harder than Product Category CRUD and should contain enough lifecycle, exception, permission, and mutable-policy behavior to exercise the chain without becoming a giant architecture exercise.

Measure primarily:

- whether modeling depth is proportional to the real domain complexity;
- whether materially different lifecycles are discovered rather than collapsed into one record/status;
- whether unresolved decisions remain visible and actually interrupt downstream design when necessary;
- whether UX journeys, states, and recovery behaviors stay aligned with business rules;
- whether data identity, integrity, and temporal semantics support those UX promises;
- whether readiness routes contradictions to the correct owning Skill rather than silently fixing them;
- whether a human can understand exactly what is being approved;
- whether approved semantics remain stable through OpenSpec specs, technical design, and tasks;
- whether planning remains distinct from apply / coding;
- whether upstream-inspired mechanisms improve behavior without adding unnecessary ceremony.

Do not grade the benchmark by requiring one canonical schema or one preferred UI. Grade decision transparency, semantic coherence, appropriate complexity, and correct gate behavior.

### Phase E — Stronger enforcement and broader confidence

Only after the first end-to-end loop is stable:

- decide whether optional CI/hook approval enforcement is worth adding;
- add milestone cross-domain benchmarks;
- improve installation/use documentation;
- refine Skill interoperability and distribution;
- periodically review mature upstream methods for transferable improvements;
- consider early-MVP review mode;
- consider a separate brownfield/reverse-engineering project or skill family if justified.

## Near-term maintenance policy

Until Phase D reveals a general issue:

- `business-domain-design v0.2.x`: compact correctness fixes only.
- `ux-flow-design v0.2.x`: compact correctness fixes only.
- `data-model-design v0.2.x`: correctness and domain-general regression fixes only.
- `design-readiness-review v0.2.x`: cross-artifact correctness and routing fixes only.
- OpenSpec schema: fix only verified orchestration, compatibility, or gate failures; do not duplicate Skill logic into schema instructions.

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

## Definition of the first meaningful milestone

The first meaningful milestone is reached when a user can provide rough meeting notes and obtain, before coding:

1. a business/domain model they can understand;
2. a page/task flow they can challenge;
3. a data model they can review;
4. a cross-artifact readiness report;
5. an explicit human approval gate;
6. orchestration that preserves those gates before tasks/apply;
7. one end-to-end realistic greenfield validation.

Items 1–6 have now been demonstrated at an experimental level, including positive and negative approval-path runtime validation. Item 7 is the current milestone work.
