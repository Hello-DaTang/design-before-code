# Design Before Code

> Before AI writes code, make business, UX, and data-model decisions reviewable by humans.

Design Before Code is a small set of Agent Skills for moving consequential product decisions **before implementation starts**.

It is greenfield-first: the input may be meeting notes, rough requirements, domain explanations, or an early product idea. Instead of letting an AI coding agent silently invent the business model, page flow, and database while coding, the workflow makes those decisions explicit as reviewable artifacts first.

The project is currently **experimental / alpha-stage**, not production-stable. The first realistic Level 3 domain benchmark has reached `READY_FOR_HUMAN_APPROVAL`; the current focus is packaging, documentation, interoperability, and release hardening rather than repeated full benchmarks.

- Project mission and anti-drift rules: [`docs/PROJECT-CHARTER.md`](docs/PROJECT-CHARTER.md)
- Roadmap: [`docs/ROADMAP.md`](docs/ROADMAP.md)
- Installation: [`docs/INSTALLATION.md`](docs/INSTALLATION.md)
- Agent compatibility: [`docs/AGENT-COMPATIBILITY.md`](docs/AGENT-COMPATIBILITY.md)
- Evaluation discipline: [`docs/EVALUATION-PROTOCOL.md`](docs/EVALUATION-PROTOCOL.md)
- Method synthesis / upstream influence map: [`docs/METHOD-SYNTHESIS.md`](docs/METHOD-SYNTHESIS.md)

## The workflow in 2 minutes

```text
rough requirements / meeting notes
        ↓
business-domain-design
        ↓
reviewable business model
        ↓
ux-flow-design
        ↓
reviewable task / page flow
        ↓
data-model-design
        ↓
reviewable data model
        ↓
design-readiness-review
        ↓
READY_FOR_HUMAN_APPROVAL
        ↓
explicit human approval
        ↓
behavior specs / technical design / tasks
        ↓
implementation
```

The core rule is simple:

> AI should not make irreversible product, UX, and data-model decisions silently during implementation.

## Quick start

### Option A — use the Skills directly

Install one or more Skill directories from [`skills/`](skills/) into the Skill discovery location supported by your agent.

The project-local layout validated with Codex is:

```text
.agents/skills/
├── business-domain-design/
├── ux-flow-design/
├── data-model-design/
└── design-readiness-review/
```

Then give the agent your requirement and ask it to run the relevant Skill instead of jumping directly into implementation.

For a full greenfield design pass, use the Skills in this order:

```text
business-domain-design
→ ux-flow-design
→ data-model-design
→ design-readiness-review
```

See [`docs/INSTALLATION.md`](docs/INSTALLATION.md) for copy/sync commands and update guidance.

### Option B — orchestrate the flow with OpenSpec

An optional custom OpenSpec schema lives at:

[`integrations/openspec/design-before-code/`](integrations/openspec/design-before-code/)

After copying the schema bundle into an initialized OpenSpec project:

```bash
openspec schema validate design-before-code
openspec new change my-feature --schema design-before-code
```

The schema orchestrates:

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
human-approval   ← human-written only
  ↓
specs
  ↓
technical-design
  ↓
tasks
  ↓
apply
```

OpenSpec is the orchestration layer, not the identity of this project. The four core Skills remain independently usable.

### See a complete small example

[`examples/quickstart/`](examples/quickstart/) contains a compact Internal Application Access Request example with the full pre-implementation chain:

```text
requirements
→ business-model
→ ux-flow
→ data-model
→ design-readiness
```

It is illustrative documentation, not another benchmark. Use it to understand artifact shape and separation of responsibilities, not as a schema or UI template to copy into unrelated products.

## Why

AI coding agents can make good implementation progress while silently deciding:

- what the real business concepts are;
- whether two concepts with different lifecycles should be merged;
- what the user must click and in what order;
- which role is allowed to perform a consequential action;
- whether one business object may have one or many downstream records;
- what becomes a table or redundant field;
- how history behaves after prices, versions, departments, standards, or policies change;
- which recommendations are treated as if stakeholders already approved them.

Design Before Code moves those choices earlier and makes them inspectable by a normal application developer, product owner, or stakeholder before code hardens them into the system.

## Current core Skills

### `business-domain-design` v0.2.2

[`skills/business-domain-design/SKILL.md`](skills/business-domain-design/SKILL.md)

Turns rough requirements and meeting notes into a reviewable business model before UX, data, API, or code design.

It includes a **Plain CRUD / Light Domain Modeling / Deep Domain Discovery** gate, optional Event Storming-lite, ubiquitous-language clarification, lifecycle/event/rule modeling, explicit prerequisite-source and calculation checks, and a reconciliation pass after human decisions are resolved.

### `ux-flow-design` v0.2.1

[`skills/ux-flow-design/SKILL.md`](skills/ux-flow-design/SKILL.md)

Turns an understood business model into a behavioral UX contract before Vue/React/Figma implementation.

It focuses on named-protagonist journeys, task-first information architecture, surface responsibilities, state/recovery coverage, low-fidelity wireframes, accessibility floor, and downstream implications. Consequential unresolved choices must remain uninstantiated, and exception/concurrency paths are checked for actual state reachability.

### `data-model-design` v0.2.3

[`skills/data-model-design/SKILL.md`](skills/data-model-design/SKILL.md)

Designs and reviews a data model through decision provenance, conceptual/logical/physical candidates, temporal review, scenario simulation, design review, and a human gate.

It emphasizes normalization, declarative integrity, temporal semantics, transitive redundancy, dual-path identity checks, canonical units, stored-derived-value consistency, reference/snapshot semantics, and human decision locks. Its own positive result is **ready for downstream technical design**, not approval to implement.

### `design-readiness-review` v0.2

[`skills/design-readiness-review/SKILL.md`](skills/design-readiness-review/SKILL.md)

Cross-reviews Business, UX, and Data artifacts before implementation.

It applies coverage lenses, decision-ledger reconciliation, lifecycle/state/event consistency, task-to-capability traceability, cross-artifact scenario walkthroughs, project-constraint re-checks, next-owner routing, and an explicit:

```text
READY_FOR_HUMAN_APPROVAL
NEEDS_DECISION
NOT_READY
```

gate.

The reviewer is a gate and router, not a universal fourth designer. It does not approve implementation on the human's behalf.

## Human approval is a real boundary

`READY_FOR_HUMAN_APPROVAL` means the artifacts are coherent enough for a human to review. It is **not** approval.

In the OpenSpec integration, `human-approval.md` is intentionally human-written. Only after a human records:

```text
APPROVAL: APPROVED
```

may the workflow continue into behavior specs, technical design, and tasks.

OpenSpec dependency edges represent artifact availability, not cryptographic or actor-authenticated approval. Organizations that need stronger enforcement should add CI/hook/review controls; the schema does not pretend to provide an identity security boundary.

## Validation evidence

### Phase B — cross-artifact smoke

The readiness loop passed the compact cross-artifact smoke suite, including contradiction routing and the separation between readiness and human approval.

### Phase C — OpenSpec orchestration

Validated locally with OpenSpec 1.8.0:

- project schema discovery and validation;
- runtime delegation to all four companion Skills;
- stop at the human-only approval artifact;
- rejection of `APPROVAL: PENDING` even after structural dependency unlock;
- acceptance of human-authored `APPROVAL: APPROVED`;
- progression through specs → technical-design → tasks;
- 9/9 planning artifacts without running `apply` or writing application code.

See [`integrations/openspec/design-before-code/VALIDATION.md`](integrations/openspec/design-before-code/VALIDATION.md).

### Phase D — realistic Level 3 benchmark

A realistic Employee Training Request + Expense Reimbursement domain benchmark exercised lifecycle boundaries, approvals, historical decision preservation, effective-dated policy, reimbursement calculation, exception flows, cardinality, and cross-artifact reconciliation.

The run deliberately used a mixed-agent handoff and exposed several generalizable failures, which were converted into small Skill corrections and focused regressions rather than benchmark-specific prompt rules.

Final result:

```text
READINESS: READY_FOR_HUMAN_APPROVAL
```

No application implementation was performed.

See [`evals/level3-training-reimbursement.md`](evals/level3-training-reimbursement.md).

## Evaluation policy

We do **not** rerun expensive full A/B experiments for every edit.

Evaluation is tiered:

1. **Unit Eval** — one compact behavior test.
2. **Regression Sample** — new eval + a few older cases.
3. **Domain Benchmark** — realistic cross-domain requirement at milestones.
4. **A/B Benchmark** — rare; reserved for major rewrites, first baselines, or release evidence when justified.

Current focused/regression artifacts live under [`evals/`](evals/). See [`docs/EVALUATION-PROTOCOL.md`](docs/EVALUATION-PROTOCOL.md) for the rules.

## Method synthesis, not prompt copying

Design Before Code is intentionally **not** a closed-door methodology and **not** a collage of copied prompts.

Its core Skills synthesize mechanisms from projects such as:

- Superpowers — collaborative design-before-implementation and explicit approval gates;
- BMad Method — complexity-sensitive planning, behavioral UX artifacts, state coverage, and readiness review;
- GitHub Spec Kit — staged artifacts, clarification/gates, explicit data-model planning, and validation scenarios;
- DDD Agent Skill — DDD-worthiness gate, Event Storming, ubiquitous language, and optional domain-boundary discovery;
- softaworks `database-schema-designer` — normalization, constraints, access-pattern-driven indexes, and deliberate denormalization.

The mechanism → adaptation map and intentional exclusions live in [`docs/METHOD-SYNTHESIS.md`](docs/METHOD-SYNTHESIS.md). Source/license acknowledgements live in [`ATTRIBUTION.md`](ATTRIBUTION.md).

## Repository structure

```text
design-before-code/
├── docs/
│   ├── PROJECT-CHARTER.md
│   ├── METHOD-SYNTHESIS.md
│   ├── EVALUATION-PROTOCOL.md
│   ├── INSTALLATION.md
│   ├── AGENT-COMPATIBILITY.md
│   └── ROADMAP.md
├── skills/
│   ├── business-domain-design/
│   ├── ux-flow-design/
│   ├── data-model-design/
│   └── design-readiness-review/
├── integrations/
│   └── openspec/
│       └── design-before-code/
├── examples/
│   ├── quickstart/
│   └── manufacturing/
├── evals/
├── ATTRIBUTION.md
├── CHANGELOG.md
├── LICENSE
└── README.md
```

## Scope and non-goals

The project is currently optimized for greenfield / 0→1 work and early MVP review while architecture is still cheap to reshape.

It is not currently intended to be:

- a full legacy-code reverse-engineering framework;
- a complete visual design system or branding workflow;
- an architecture generator;
- a replacement for behavior specs, technical design, or implementation planning;
- a guarantee that an AI agent cannot disobey instructions;
- a substitute for organization-level approval controls.

## Status

**Experimental / alpha-stage.**

The first meaningful milestone is complete: all four core Skills exist, OpenSpec orchestration and approval behavior have been validated, and the first realistic Level 3 greenfield benchmark reached `READY_FOR_HUMAN_APPROVAL` without entering implementation.

Current priority: installation clarity, agent interoperability, quick-start examples, release hygiene, and an initial alpha release. Another expensive Level 3 benchmark is **not** the immediate next step.