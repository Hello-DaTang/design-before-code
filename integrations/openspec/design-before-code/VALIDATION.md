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

## 4. Observed integration correction

The first runtime data-model artifact ended with the phrase `Ready to implement`, inherited from `data-model-design v0.2.1`.

That wording was too strong inside the multi-artifact workflow because data-model readiness is not cross-artifact readiness and is not human approval.

The Skill was corrected so its standalone positive result is now scoped to downstream design rather than implementation permission. A focused regression case was added instead of rerunning the full benchmark.

## Remaining Phase C validation

One positive approval-path smoke test remains:

1. a human changes `human-approval.md` to an explicit `APPROVAL: APPROVED` and records reviewer/time;
2. `specs` is generated from the approved artifacts;
3. `technical-design` is generated after specs;
4. `tasks` is generated after technical design;
5. the test stops before `apply` and confirms no implementation was started.

If this path passes, Phase C OpenSpec orchestration can be considered complete at the experimental level and the project can move to its first Level 3 end-to-end domain benchmark.
