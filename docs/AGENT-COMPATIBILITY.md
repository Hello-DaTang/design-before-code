# Agent Compatibility

Design Before Code is intended to be **agent-agnostic at the method level**.

The canonical artifacts are ordinary Markdown Skill directories under [`../skills/`](../skills/), and the OpenSpec bundle is ordinary project-local schema content. However, Skill discovery, invocation semantics, tool permissions, and persistence differ across AI coding agents.

This document distinguishes **observed compatibility** from **expected compatibility**. It intentionally does not claim support for agents that have not been exercised.

## Compatibility levels

| Level | Meaning |
|---|---|
| **Verified** | The agent was exercised against the relevant Design Before Code workflow and the observed behavior was recorded. |
| **Interoperability verified** | The agent successfully continued from artifacts produced by another agent, but was not independently tested through the entire clean-start workflow. |
| **Structurally compatible / unverified** | The project format should be portable, but no project evidence currently supports a stronger claim. |

## Current evidence

### Codex — Verified

Observed project evidence includes:

- project-local Skill discovery using `.agents/skills/<skill-name>/` during the current benchmark setup;
- execution of Design Before Code Skills rather than silent fallback;
- OpenSpec Phase C runtime delegation to all four core Skills;
- stop at `READY_FOR_HUMAN_APPROVAL` without agent-authored approval;
- semantic rejection of `APPROVAL: PENDING`;
- progression after human-authored `APPROVAL: APPROVED` through specs → technical design → tasks;
- planning completion without running `apply` or writing application code.

The Phase D Level 3 benchmark also used Codex for the early Business/domain portion before the run continued with a second coding agent.

Evidence:

- [`../integrations/openspec/design-before-code/VALIDATION.md`](../integrations/openspec/design-before-code/VALIDATION.md)
- [`../evals/level3-training-reimbursement.md`](../evals/level3-training-reimbursement.md)

### Mixed-agent continuation — Interoperability verified

The Phase D Level 3 benchmark intentionally became a mixed-agent run after the first agent's quota was exhausted.

The second coding agent successfully continued from repository artifacts already on disk rather than requiring the whole reasoning history to be recreated. It performed later Business/UX reconciliation, Data modeling, focused corrections, and the final design-readiness pass.

This is meaningful interoperability evidence because Design Before Code relies on explicit artifacts rather than hidden conversation state.

It does **not** prove that every coding agent will discover or invoke Skills using the same directory convention or instruction mechanism.

See [`../evals/level3-training-reimbursement.md`](../evals/level3-training-reimbursement.md).

## GitHub Copilot / VS Code agent workflows

The project has practical mixed-agent continuation evidence from the current development workflow, but we do not yet treat GitHub Copilot as independently verified through a clean-start, full OpenSpec 0→planning-complete run.

Recommended status:

**Interoperability verified; full standalone workflow not yet separately benchmarked.**

When using Copilot or another VS Code agent, ensure that:

1. the agent can actually read the installed Skill content;
2. the relevant Skill is explicitly invoked or its instructions are made available through the agent's supported mechanism;
3. the agent reads existing Business/UX/Data artifacts from disk before continuing;
4. unresolved decisions remain unresolved rather than being filled from conversation guesses;
5. human approval remains human-only.

If the agent does not natively discover `.agents/skills/`, map the canonical [`../skills/`](../skills/) directories into whatever instruction/Skill mechanism that agent supports. Do not assume Codex's tested discovery path is universal.

## Claude Code, Cursor, Gemini CLI, and other agents

Current status:

**Structurally compatible / unverified.**

The Skills are intentionally written as portable Markdown reasoning workflows and do not depend on a proprietary implementation API. That makes portability plausible, but it is not evidence of runtime compatibility.

Before claiming support for another agent, verify at least:

- how that agent discovers project-local Skills/instructions;
- whether referenced files beside `SKILL.md` remain available;
- whether it obeys `DECISION REQUIRED` / human-gate semantics;
- whether it can continue correctly from artifacts created by another agent;
- whether it refrains from silently replacing an unavailable Skill with generic reasoning;
- whether it preserves the human-only approval boundary.

A small focused compatibility check is preferred over immediately rerunning the entire Level 3 benchmark.

## What is agent-agnostic

These parts of the project are intentionally independent of a particular coding agent:

- Business / UX / Data / readiness artifact semantics;
- `FACT / INFERENCE / RECOMMENDATION / ASSUMPTION / DECISION REQUIRED` provenance discipline;
- human decision reconciliation;
- state/lifecycle/cardinality review rules;
- temporal and redundancy reasoning;
- readiness outcomes;
- the requirement that the agent cannot self-approve consequential human decisions;
- the OpenSpec schema files themselves.

## What is agent-specific

Expect differences in:

- Skill discovery directories;
- slash commands or explicit invocation syntax;
- whether project-local instructions are loaded automatically;
- tool permissions and filesystem write behavior;
- session/context persistence;
- how an agent reports or enforces stop conditions;
- support for OpenSpec-specific command integrations.

Design Before Code should adapt to those surfaces without changing the underlying product-design semantics.

## Compatibility testing policy

Do not create an expensive per-agent benchmark matrix by default.

For a new agent, start with a compact check:

1. install one core Skill;
2. verify the agent actually reads it;
3. run one small decision-gating case;
4. verify it writes/continues from a disk artifact;
5. verify it stops rather than silently resolving a human-owned decision.

Only promote the agent to **Verified** after enough evidence exists for the claim being made.

This follows [`EVALUATION-PROTOCOL.md`](EVALUATION-PROTOCOL.md): compatibility evidence should be proportional to the risk and release claim, not an excuse for repeated expensive E2E runs.
