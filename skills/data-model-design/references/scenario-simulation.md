# Scenario Simulation

Scenario simulation validates whether a proposed model preserves business meaning under realistic change.

## Goal

Do not merely prove that rows can be inserted. Prove that the model still tells the correct story after time passes and related data changes.

## Procedure

1. Pick a realistic business case with concrete names, dates, versions, quantities, and state changes.
2. Write the event sequence in business language.
3. Map each event to conceptual/logical entities.
4. Show the key records that would exist after each step.
5. Change one consequential mutable fact later.
6. Query the old business event again.
7. Compare the displayed historical result with what a user expects.
8. If the model cannot preserve the expected result, revise the design.

## Recommended stressors

Include at least one of these when relevant:

- master-data rename;
- product/version change;
- price/rate change;
- ownership or department change;
- deletion/deactivation;
- correction of reference data;
- repeated import;
- concurrent edit;
- duplicate business input;
- changing classification or hierarchy.

## Output template

### Scenario

Describe the real-world sequence.

### Expected business truth

State what must remain true after later changes.

### Record evolution

Show only the important records and fields, preferably in small tables.

### Mutation test

Change a relevant current value and explain what historical queries now return.

### Result

Choose one:

- PASS — the model preserves intended semantics.
- FAIL — the model rewrites or loses intended semantics.
- NEEDS DECISION — the business policy is not yet known.

### Required design change

If FAIL, describe the smallest conceptual/logical correction before changing the physical schema.

## Example question pattern

A useful simulation asks questions such as:

> A production record used product version V1 and standard workhour 20 seconds on September 5. V2 becomes current on September 10 with 18 seconds. If V1's master data is later edited, what should the September 5 record display, and can the model guarantee that result?

The purpose is to expose hidden temporal and ownership assumptions before implementation.
