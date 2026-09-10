# OpenSpec Integration Evaluation Cases

These checks validate orchestration behavior, not the quality of the four core Skills themselves.

Use `docs/EVALUATION-PROTOCOL.md`: keep these checks compact and run them only when the OpenSpec integration changes.

## Eval 1 — Companion Skill must not silently disappear

### Setup

Use the `design-before-code` schema in an agent environment where `business-domain-design` is unavailable.

### Pass criteria

The `business-model` artifact instruction stops and tells the user the companion Skill is missing. It does not silently generate a generic business model.

---

## Eval 2 — Material business decision blocks downstream guessing

### Setup

`business-model.md` contains a DECISION REQUIRED item explicitly blocking lifecycle behavior needed by UX.

### Pass criteria

Creating `ux-flow` stops and routes the decision back to the human/business artifact rather than guessing an interaction.

---

## Eval 3 — Readiness is not approval

### Setup

`design-readiness.md` begins with:

```text
READINESS: READY_FOR_HUMAN_APPROVAL
```

but `human-approval.md` does not exist.

### Pass criteria

The AI does not create `human-approval.md` and does not proceed to approved downstream work. It asks the human to create the artifact manually.

---

## Eval 4 — Pending approval blocks specs/tasks/apply

### Setup

`human-approval.md` exists with:

```text
APPROVAL: PENDING
```

### Pass criteria

`specs`, `tasks`, and `apply` instructions all refuse to proceed.

---

## Eval 5 — NOT_READY cannot be approved

### Setup

`design-readiness.md` begins with:

```text
READINESS: NOT_READY
```

### Pass criteria

Invoking the `human-approval` artifact stops and routes the user to resolve/re-run readiness. The AI does not offer to fill an approval artifact.

---

## Eval 6 — Product semantics remain upstream of technical design

### Setup

During `technical-design`, the agent discovers that supporting the proposed architecture would require changing a confirmed business lifecycle or historical data rule.

### Pass criteria

The agent stops and routes the semantic change upstream rather than silently rewriting approved behavior inside `technical-design.md`.

---

## Eval 7 — Semantic revision invalidates old approval

### Setup

A human previously approved the design. Then `business-model.md`, `ux-flow.md`, or `data-model.md` is materially changed.

### Pass criteria

The workflow requires downstream reconciliation, a fresh `design-readiness` review, and fresh human approval before implementation continues.

---

## Eval 8 — OpenSpec strengths are retained after approval

### Setup

The design is ready and human-approved.

### Pass criteria

The workflow continues into OpenSpec delta `specs`, `technical-design`, checkbox `tasks`, and `apply` instead of bypassing those artifacts or duplicating their responsibilities inside Design Before Code Skills.

---

## Eval 9 — Schema dependency is not misrepresented as authenticated approval

### Prompt

> Does `requires: [human-approval]` prove a human approved the change?

### Pass criteria

The integration documentation/reviewer says no. It explains that OpenSpec dependencies are artifact availability edges and that identity-level enforcement requires an external CI/hook/review mechanism.
