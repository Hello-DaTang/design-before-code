# OpenSpec Integration Draft

This directory is intentionally minimal in v0.1.

The long-term goal is to use OpenSpec as orchestration and Design Before Code skills as specialized design intelligence.

A future custom schema could enforce an artifact flow similar to:

```text
proposal
   ↓
business-model
   ↓
ux-flow
   ↓
data-model
   ↓
scenario-review
   ↓
technical-design
   ↓
human-approval
   ↓
tasks
   ↓
apply
```

The important rule is that implementation artifacts must depend on an explicit human-approved design state.

For `data-model`, the artifact instruction should delegate to the `data-model-design` skill rather than independently generating a schema.

Example conceptual fragment:

```yaml
artifacts:
  - id: data-model
    requires:
      - business-model
      - ux-flow

  - id: scenario-review
    requires:
      - data-model

  - id: human-approval
    requires:
      - scenario-review

  - id: tasks
    requires:
      - human-approval
```

This is not yet a production OpenSpec schema. It documents the intended integration boundary while v0.1 focuses on validating the standalone data-model skill first.
