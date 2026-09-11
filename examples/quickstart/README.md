# Quick Start Example — Internal Application Access Request

This directory shows what a small end-to-end Design Before Code result looks like before implementation.

It is an **illustrative example**, not a benchmark and not a canonical architecture. The files are intentionally compact so a new user can understand the artifact chain without reading a large domain case.

## Scenario

An employee requests access to one internal application. Their manager approves or rejects the request. After approval, an IT operator grants the approved access level. The employee may cancel the request before access is granted.

Out of scope:

- automatic provisioning;
- SSO / identity-provider integration;
- periodic access recertification;
- multi-stage approvals;
- revocation after access has been granted.

## Artifact chain

```text
requirements.md
    ↓
business-model.md
    ↓
ux-flow.md
    ↓
data-model.md
    ↓
design-readiness.md
```

The example demonstrates the core separation of concerns:

- [`requirements.md`](requirements.md) — what the stakeholder asked for;
- [`business-model.md`](business-model.md) — business meaning, lifecycle, rules, and decision provenance;
- [`ux-flow.md`](ux-flow.md) — how Employee, Manager, and IT Operator complete their tasks;
- [`data-model.md`](data-model.md) — identities, relationships, integrity, and history needed to support that behavior;
- [`design-readiness.md`](design-readiness.md) — cross-artifact review before human approval.

The final readiness result is:

```text
READINESS: READY_FOR_HUMAN_APPROVAL
```

That is **not** approval to implement. A human reviewer still owns the approval decision.

## How to use this example

Do not copy the entities or pages into another product. Use the example to understand the shape and responsibilities of each artifact, then run the Skills against your own requirement.

For installation and invocation guidance, see [`../../docs/INSTALLATION.md`](../../docs/INSTALLATION.md).
