# OpenSpec schema — design-before-code

This bundle integrates the four Design Before Code skills with OpenSpec's artifact workflow.

## Flow

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

The schema deliberately keeps OpenSpec's strengths after the design gate: delta behavior specs, technical design, checkbox task tracking, apply, verify, and archive workflows remain OpenSpec responsibilities.

## Validation status

Validated locally with **OpenSpec 1.8.0** on 2026-09-10.

Schema/CLI checks passed against a clean temporary OpenSpec project:

```text
openspec schema which --all
→ project schema: design-before-code
→ package schema: spec-driven

openspec schema validate design-before-code --verbose
→ YAML parsed
→ schema structure valid
→ template files found
→ dependency graph validation passed

openspec schema which design-before-code
→ Source: project

openspec new change dbc-smoke --schema design-before-code
→ change created successfully
```

Runtime validation also passed at the current experimental level:

- all four companion Skills were actually delegated to;
- the agent stopped at `READY_FOR_HUMAN_APPROVAL` without creating/modifying `human-approval.md`;
- `APPROVAL: PENDING` remained semantically blocked even after OpenSpec structurally unlocked specs;
- human-authored `APPROVAL: APPROVED` allowed specs → technical-design → tasks;
- planning reached 9/9 artifacts without running `apply` or writing application code.

Observed dependency graph:

```text
proposal
└─ business-model
   └─ ux-flow
      └─ data-model
         └─ design-readiness
            └─ human-approval
               └─ specs
                  └─ technical-design
                     └─ tasks
```

`data-model` also requires `business-model`; `technical-design` also requires `data-model` and `human-approval`; `tasks` also requires `human-approval` and `specs`.

Detailed evidence is recorded in [`VALIDATION.md`](VALIDATION.md).

The later Phase D Level 3 domain benchmark also reached `READY_FOR_HUMAN_APPROVAL`; that benchmark validates the design method rather than adding a new OpenSpec schema claim. See [`../../../evals/level3-training-reimbursement.md`](../../../evals/level3-training-reimbursement.md).

## Required companion skills

The agent environment must expose:

- `business-domain-design`
- `ux-flow-design`
- `data-model-design`
- `design-readiness-review`

Artifact instructions precheck these skills and stop instead of silently falling back when they are unavailable.

## Installation

Your target project must already be initialized with OpenSpec.

From the target project root, copy this directory to:

```text
openspec/schemas/design-before-code/
```

Then either use it per change:

```bash
openspec new change my-feature --schema design-before-code
```

or set it as the project default in `openspec/config.yaml`:

```yaml
schema: design-before-code
```

Validate after copying:

```bash
openspec schema validate design-before-code
openspec schema which design-before-code
```

For Skill installation and update guidance, see [`../../../docs/INSTALLATION.md`](../../../docs/INSTALLATION.md).

## Human approval semantics

`human-approval.md` is intentionally a **human-only artifact**.

The AI instructions must not create or modify it. A human creates it only after `design-readiness.md` reports:

```text
READINESS: READY_FOR_HUMAN_APPROVAL
```

The human then sets:

```text
APPROVAL: APPROVED
```

Only approved designs may proceed to specs, technical design, tasks, or apply.

### Important limitation

OpenSpec currently treats artifact dependencies as file/dependency availability, not actor-authenticated approval gates. It cannot prove that `human-approval.md` was actually authored by a human. The schema therefore provides an **agent/workflow-level gate**, not a security boundary.

For organization-level enforcement, add an external CI/hook/review mechanism that verifies approval provenance before tasks/apply. Do not claim the schema alone provides cryptographic or identity-level approval enforcement.

## Recommended operation style

Prefer `/opsx:continue` while the design is being reviewed. Do not use a fast-forward workflow to bypass human decisions.

If a Design Before Code artifact contains a material `DECISION REQUIRED`, resolve it in the owning artifact, then regenerate downstream artifacts as needed. After any semantic change, re-run `design-readiness` and obtain fresh human approval.

See `INTEGRATION.md` for detailed lifecycle behavior and design rationale.
