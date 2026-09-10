# Design Before Code — Evaluation Protocol

This document defines how we evaluate skills without repeatedly paying the cost of full A/B experiments, fresh sessions, memory resets, and long end-to-end outputs.

The protocol exists to keep evaluation useful, cheap, and repeatable.

## Principles

1. **Do not rerun a no-skill baseline for every change.** Historical baselines are frozen evidence unless a major architecture change makes them obsolete.
2. **Prefer the smallest eval that can falsify the behavior being changed.**
3. **Do not require one canonical schema or flow.** Evaluate whether important decisions become visible, reasoned about, and reviewable.
4. **Promote real failures into regression cases.** A discovered failure should become a compact eval before the core skill is changed.
5. **Use full domain benchmarks only at milestones.** Do not spend full-session cost on every small edit.
6. **Cross-domain validation matters more than repeatedly optimizing one benchmark.**

## Four evaluation levels

### Level 1 — Unit Eval

Use for routine skill edits.

- One focused prompt.
- Usually 100–400 words.
- Tests one behavior.
- Run only the skill-enabled agent.
- Expected output should be short enough to inspect manually.

Example:

> `product_version` already determines `product_id`. A proposed record stores both. Review the design.

Pass criteria can check whether the agent identifies transitive redundancy, contradiction risk, source of truth, and declarative integrity options.

### Level 2 — Regression Sample

Use after a meaningful skill change.

- Run the new/changed eval.
- Randomly select 2–4 older regression cases.
- Do not rerun the full suite unless the change is broad.
- Compare against explicit pass criteria, not a no-skill agent.

A regression sample is enough for most patch releases.

### Level 3 — Domain Benchmark

Use at milestones such as a planned minor release or after major reasoning changes.

- One realistic multi-section requirement.
- Prefer a domain different from the benchmark that motivated the recent changes.
- Run the skill-enabled agent only by default.
- Evaluate conceptual modeling, lifecycle, temporal behavior, redundancy, scenario simulation, and human review gate together.

Examples of domains:

- manufacturing;
- ecommerce/orders/refunds;
- HR/employee assignment;
- approval/workflow;
- finance/budgeting.

### Level 4 — A/B Benchmark

Use rarely.

Run the same requirement with and without the skill only when:

- creating the first baseline for a new major skill;
- making a major architecture rewrite;
- questioning whether the skill still adds value over the base model;
- preparing evidence for a major/public release.

Do not require memory deletion or elaborate environment cleanup for routine development. Isolation should be proportional to the decision being made.

## Evaluation workflow

```text
observed failure or desired behavior
              ↓
is it domain-general?
       ├─ no → keep as domain example / do not change core skill
       └─ yes
              ↓
write a minimal eval + pass criteria
              ↓
change the skill
              ↓
run Level 1 eval
              ↓
run 2–4 sampled regressions
              ↓
pass? ── no → revise
  │
 yes
  ↓
merge / continue
```

At a milestone:

```text
stable unit/regression behavior
          ↓
new-domain benchmark
          ↓
review failures
          ↓
only generalize cross-domain failures into the core skill
```

## Pass/fail style

Prefer explicit behavior criteria such as:

- identifies an unresolved business decision;
- distinguishes FACT from RECOMMENDATION;
- detects a derivable duplicate;
- distinguishes business-effective time from recording time;
- compares materially different model semantics;
- blocks unresolved physical choices;
- avoids an unnecessary entity;
- explains the design in language a normal application developer can challenge.

Avoid criteria such as:

- must create exactly 5 tables;
- must use a particular architecture;
- must choose snapshot instead of effective-dated history;
- must match the author’s preferred schema.

## Token-cost rule

Use the cheapest level that can answer the current engineering question.

A small skill patch should normally cost:

- 1 new unit eval;
- 2–4 sampled regressions;
- no fresh no-skill baseline;
- no full domain benchmark.

Full A/B work is evidence gathering, not the default development loop.

## Release guidance

Suggested defaults:

- **Patch (`v0.x.y`)**: Level 1 + sampled Level 2.
- **Minor (`v0.x`)**: Level 2 + one Level 3 cross-domain benchmark.
- **Major / public confidence milestone**: broader Level 2 + multiple Level 3 benchmarks; Level 4 only if value-over-baseline needs revalidation.

## Anti-drift link

All eval additions and skill changes must also satisfy `docs/PROJECT-CHARTER.md`.

The evaluation suite protects behavior from regression. The Project Charter protects the product from mission drift.
