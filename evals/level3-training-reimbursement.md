# Level 3 Domain Benchmark — Training Request + Expense Reimbursement

## Purpose

Record the first realistic Level 3 end-to-end benchmark for Design Before Code. This is regression evidence, not a canonical product design.

## Domain

Employee Training Request + Training Expense Reimbursement.

The case exercised:

- pre-approval before training;
- cancellation timing;
- post-training expense claims;
- manager overage re-approval;
- Finance reject/correct/resubmit history;
- effective-dated reimbursement policy;
- historical calculation explainability;
- payment-result recording;
- explicit human decisions and cross-artifact readiness.

## Run shape

- Greenfield / 0→1.
- Mixed-agent E2E run.
  - Early Business work: Codex.
  - Later UX, Data, reconciliation, and Readiness continuation: second agent.
- OpenSpec schema: `design-before-code`.
- No no-skill baseline.
- No A/B run.
- No memory/session clearing.
- No apply/code.
- Product Category smoke was not rerun.

## Human decisions

The benchmark intentionally exposed material choices rather than allowing agents to guess.

First decision set: D1–D5.

- prerequisite fact source/timing;
- policy effective point;
- reimbursement formula/rounding;
- actual expense above approved estimate;
- recovery after Finance rejection.

Second decision set: N1–N4, discovered during downstream design.

- outcome after overage re-approval decline;
- payment recording role/status vocabulary;
- no-policy gap behavior;
- claim multiplicity.

All nine were resolved by the human stakeholder before downstream design was allowed to treat them as FACT.

## General findings

### Skill-method defects that justified compact Skill changes

1. **Resolved-decision reconciliation drift**
   - A human decision could be marked RESOLVED in one section while active lifecycle/scenario/downstream text still treated it as open.
   - Generalized into `business-domain-design v0.2.2`: mandatory post-human-decision semantic reconciliation.

2. **UX unresolved decision silently instantiated**
   - UX could label ownership/multiplicity unresolved while assigning one concrete answer elsewhere.
   - Generalized into `ux-flow-design v0.2.1`: unresolved means uninstantiated.

3. **UX unreachable recovery states**
   - Some exception/concurrency paths were impossible under the already-defined lifecycle/time gates.
   - Generalized into `ux-flow-design v0.2.1`: reachability / gate-algebra review.

4. **Data dual-path identity / transitive dependency miss**
   - Two stored relationship paths could identify the same business object without an agreement invariant.
   - Generalized into `data-model-design v0.2.3`: mandatory dual-path identity check, plus stronger stored-derived-value and reference+snapshot review.

### Earlier Business correctness finding

The benchmark also exposed two domain-general Business ambiguities already incorporated in `business-domain-design v0.2.1`:

- prerequisite facts need an explicit source/timing when eligibility depends on them;
- named calculation parameters do not determine formula composition/order/rounding.

### Agent execution / reconciliation defects

These were evidence of execution quality issues, not reasons to encode benchmark-specific rules into core Skills:

- stale resolved/unresolved wording after targeted edits;
- wording/example inconsistencies;
- minor downstream documentation gaps;
- one first-pass Data redundancy walk that missed a relationship-path contradiction despite the broader redundancy principle already existing.

### Readiness behavior that worked as intended

`design-readiness-review v0.2` correctly:

- compared artifacts rather than summarizing them;
- detected a stale resolved/unresolved contradiction;
- detected unreachable UX states;
- routed findings to the owning artifact/Skill;
- withheld readiness while a blocker remained;
- returned `READY_FOR_HUMAN_APPROVAL` after the blocker and material UX defect were corrected.

No method defect was found that justified a readiness Skill version bump.

## Final result

```text
READINESS: READY_FOR_HUMAN_APPROVAL
```

The benchmark stopped there.

No human approval artifact, specs, technical design, tasks, apply step, or application code was required for this Phase D validation because Phase C had already validated the downstream OpenSpec approval/orchestration path separately.

## Evaluation policy conclusion

The benchmark achieved its purpose. Do not rerun this domain after compact Skill fixes unless a future method change specifically invalidates the evidence.

Follow-up validation for v0.2.x fixes should use focused regression definitions, not another paid Level 3 E2E run.