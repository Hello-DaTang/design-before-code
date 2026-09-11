# OpenSpec Integration Validation

This file records observed validation evidence for the `design-before-code` custom OpenSpec schema.

## Environment

- OpenSpec CLI: `1.8.0`
- Platform used for smoke tests: Windows PowerShell + ChatGPT Desktop Codex mode
- Test project: temporary initialized OpenSpec project
- Schema location under test: `openspec/schemas/design-before-code/`

## 1. CLI schema validation — PASS

Observed commands:

```powershell
openspec schema which --all
openspec schema validate design-before-code --verbose
openspec schema which design-before-code
openspec new change dbc-smoke --schema design-before-code
openspec status --change dbc-smoke
```

Observed results:

- `design-before-code` was discovered as a project schema.
- `schema.yaml` parsed successfully.
- schema structure validation passed.
- all referenced template files were found.
- dependency-graph validation passed.
- change `dbc-smoke` was created successfully using the custom schema.

Initial artifact graph behaved as intended:

```text
proposal              ready
business-model        blocked by proposal
ux-flow               blocked by business-model
data-model             blocked by business-model, ux-flow
design-readiness       blocked by business-model, ux-flow, data-model
human-approval         blocked by design-readiness
specs                  blocked by human-approval
technical-design       blocked by human-approval, specs, data-model
tasks                  blocked by human-approval, specs, technical-design
```

## 2. Runtime Skill delegation and human-stop behavior — PASS

A controlled Product Category maintenance requirement was run through the existing `dbc-smoke` change.

Observed artifact creation:

- `proposal.md`
- `business-model.md`
- `ux-flow.md`
- `data-model.md`
- `design-readiness.md`

Observed Skill delegation:

- `business-domain-design` → `business-model.md`
- `ux-flow-design` → `ux-flow.md`
- `data-model-design` → `data-model.md`
- `design-readiness-review` → `design-readiness.md`

Observed readiness result:

```text
READINESS: READY_FOR_HUMAN_APPROVAL
```

The agent stopped at the human gate and did **not** create or modify `human-approval.md`.

OpenSpec status at the stop point showed 5/9 artifacts complete, with `human-approval` ready and all post-approval artifacts still blocked.

## 3. Negative semantic approval gate — PASS

A human manually created `human-approval.md` with:

```text
APPROVAL: PENDING
```

As expected, OpenSpec's structural dependency graph then considered `human-approval` complete and `specs` structurally ready.

The runtime agent then:

- read the actual approval value;
- observed `APPROVAL: PENDING`;
- followed the schema's semantic approval check;
- refused to create specs;
- did not modify `human-approval.md`;
- did not proceed to technical design, tasks, or implementation.

This confirms the intended two-layer behavior:

```text
OpenSpec structural readiness: READY
Design Before Code semantic approval gate: BLOCKED
```

Artifact existence alone is therefore not treated as product approval by the agent workflow.

## 4. Positive semantic approval path — PASS

The human reviewer then changed the approval artifact manually to:

```text
APPROVAL: APPROVED
```

and recorded reviewer/time information.

The runtime agent re-read both gate artifacts and observed:

```text
READINESS: READY_FOR_HUMAN_APPROVAL
APPROVAL: APPROVED
```

It then proceeded one artifact at a time through the approved post-gate planning path:

- `specs/product-category-maintenance.md`
- `technical-design.md`
- `tasks.md`

Observed behavior:

- specs were derived from the approved Business / UX / Data design;
- technical design did not change approved product semantics;
- tasks were generated only after specs and technical design existed;
- `human-approval.md` was not modified by the agent;
- `apply` was not executed;
- no application source code was created or modified.

Final OpenSpec status:

```text
Progress: 9/9 artifacts complete

[x] proposal
[x] business-model
[x] ux-flow
[x] data-model
[x] design-readiness
[x] human-approval
[x] specs
[x] technical-design
[x] tasks
```

This validates the intended planning boundary:

```text
missing approval → blocked
PENDING approval → semantically blocked
human APPROVED → specs → technical-design → tasks
planning complete ≠ apply / implementation started
```

## 5. Observed integration correction

The first runtime data-model artifact ended with the phrase `Ready to implement`, inherited from `data-model-design v0.2.1`.

That wording was too strong inside the multi-artifact workflow because data-model readiness is not cross-artifact readiness and is not human approval.

The Skill was corrected in `data-model-design v0.2.2` so its standalone positive result is scoped to downstream technical design rather than implementation permission. A focused regression case was added instead of rerunning the full benchmark.

## Phase C result

**PASS at the current experimental level.**

Validated with OpenSpec 1.8.0:

1. schema discovery and parsing;
2. template and dependency-graph validation;
3. runtime delegation to all four Design Before Code Skills;
4. stop at the human-only approval artifact;
5. semantic rejection of `APPROVAL: PENDING` even when OpenSpec structurally unlocks specs;
6. semantic acceptance of a human-authored `APPROVAL: APPROVED`;
7. approved progression through specs → technical design → tasks;
8. separation of planning completion from `apply` / coding.

The schema still does **not** provide actor-authenticated or cryptographic proof that an approval file was written by a human. Stronger organizational enforcement remains an optional future CI/hook/review concern.

## Follow-up milestone status

The next planned milestone at the time of the Phase C validation was a realistic Level 3 greenfield domain benchmark.

That milestone has now been completed in Phase D. The mixed-agent Employee Training Request + Expense Reimbursement benchmark reached:

```text
READINESS: READY_FOR_HUMAN_APPROVAL
```

without entering application implementation. See [`../../../evals/level3-training-reimbursement.md`](../../../evals/level3-training-reimbursement.md).

Current work has therefore moved to packaging, documentation, interoperability, and alpha release hardening rather than another immediate Level 3 run.
