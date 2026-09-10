# Scenario Simulation

Scenario simulation validates whether a proposed model preserves business meaning under realistic change.

## Goal

Do not merely prove that rows can be inserted. Prove that the model still tells the correct story after time passes, related data changes, and data is entered or corrected at inconvenient times.

## Procedure

1. Pick a realistic business case with concrete names, dates, versions, quantities, and state changes.
2. Write the event sequence in business language.
3. Distinguish **business-effective time** from **system recording time** when they can differ.
4. Map each event to conceptual/logical entities.
5. Show the key records that would exist after each step.
6. Change one consequential mutable fact later.
7. Query the old business event again.
8. Add one adversarial timing mutation when relevant: late entry, backdated correction, delayed import, or delayed approval.
9. Compare the displayed/calculated historical result with what a user expects.
10. If the model cannot preserve the expected result, revise the design or mark the policy as a human decision.

## Recommended stressors

Include at least one of these when relevant:

- master-data rename;
- product/version change;
- price/rate/standard change;
- ownership or department change;
- deletion/deactivation;
- correction of reference data;
- repeated import;
- concurrent edit;
- duplicate business input;
- changing classification or hierarchy.

When the domain has mutable ownership, organization, price, rate, standards, or versions, also include at least one **timing adversary**:

- event occurs → related master data changes → event is entered late;
- event occurs → relationship changes → event is corrected backdated;
- data is imported after the effective version changed;
- approval happens after the effective business date;
- a previously issued report is rerun after master-data mutation.

## Output template

### Scenario

Describe the real-world sequence with concrete dates.

### Time semantics

For important steps state:

| Step | Business-effective time | System recording time |
|---|---|---|
| Production occurred | 2026-09-01 | 2026-09-10 |
| Employee transferred | 2026-09-05 | 2026-09-05 |

Use this only when the distinction matters.

### Expected business truth

State what must remain true after later changes.

### Record evolution

Show only the important records and fields, preferably in small tables.

### Mutation test

Change a relevant current value and explain what historical queries now return.

### Late-entry / backdated test

When relevant, verify that the model resolves historical relationships/values using the correct business-effective time rather than blindly copying current state at insertion time.

Example:

1. September 1: Zhang belongs to Department A and performs production work.
2. September 5: Zhang transfers to Department B.
3. September 10: the September 1 record is entered.
4. Expected: the September 1 production remains attributed to Department A.

If the proposed model simply copies Zhang's current department on September 10, mark the scenario **FAIL**.

### Result

Choose one:

- PASS — the model preserves intended semantics.
- FAIL — the model rewrites, contradicts, or loses intended semantics.
- NEEDS DECISION — the business policy is not yet known.

### Required design change

If FAIL, describe the smallest conceptual/logical correction before changing the physical schema.

## Example question patterns

### Version and standard mutation

> A production record used product version V1 and standard workhour 20 seconds on September 5. V2 becomes current on September 10 with 18 seconds. If V1's master data is later edited, what should the September 5 record display, and can the model guarantee that result?

### Late organizational entry

> An employee belonged to Department A when production happened on September 1, transferred to Department B on September 5, and the September 1 production record was entered on September 10. Which department should the historical record use, and can the model obtain it without relying on the employee's current department?

### Derived redundancy contradiction

> A production record stores both `product_id` and `product_version_id`. The selected version belongs to a different product than the stored `product_id`. Can this contradictory row exist, and which value would downstream reports trust?

The purpose is to expose hidden temporal, dependency, ownership, and decision assumptions before implementation.
