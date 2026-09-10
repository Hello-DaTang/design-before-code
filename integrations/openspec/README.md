# OpenSpec Integration

Design Before Code uses OpenSpec as an orchestration layer, not as the product identity.

The first actual custom schema bundle is now available at:

```text
integrations/openspec/design-before-code/
```

It contains:

```text
design-before-code/
├── schema.yaml
├── README.md
├── INTEGRATION.md
└── templates/
    ├── proposal.md
    ├── business-model.md
    ├── ux-flow.md
    ├── data-model.md
    ├── design-readiness.md
    ├── human-approval.md
    ├── spec.md
    ├── technical-design.md
    └── tasks.md
```

## Workflow

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

The first four design artifacts delegate to the standalone Design Before Code skills. After human approval, OpenSpec resumes its native strengths: behavior specs, technical design, task tracking, apply, verify, and archive.

## Approval boundary

OpenSpec dependency edges control artifact availability; they are not actor-authenticated business gates. Therefore the schema treats `human-approval.md` as a human-only contract and repeats exact approval checks in specs, tasks, and apply instructions.

This is an agent/workflow-level gate. Teams that need identity-level enforcement should add an external CI/hook/review mechanism.

See [`design-before-code/README.md`](design-before-code/README.md) for installation and [`design-before-code/INTEGRATION.md`](design-before-code/INTEGRATION.md) for lifecycle details.
