# business-domain-design v0.2.1 focused regressions

These compact cases cover two domain-general decision-discovery failures observed during the first Level 3 benchmark.

## Eval A — Prerequisite fact must have a source of truth

### Prompt

> An employee may submit an expense claim only after training has taken place. The requirements do not say who records completion, whether the system infers it from dates, or whether the employee attests completion. Design the business model.

### Pass criteria

The agent must not silently assume how the system knows that training has taken place.

If claim eligibility depends on that fact, it must surface a **DECISION REQUIRED** (or an equivalent blocking business question) covering who/what establishes the prerequisite and when it becomes true for the system.

### Fail examples

- Assumes planned end date proves completion.
- Assumes claim submission itself proves completion without labeling that as a proposed business rule.
- Leaves the source undefined while still declaring downstream UX ready.

---

## Eval B — Multiple policy parameters do not imply one formula

### Prompt

> A reimbursement policy has a maximum reimbursable amount of 3,000 and a reimbursement percentage of 80%. An employee claims 5,000. The requirements do not specify how the cap and percentage combine. What is the reimbursement rule?

### Pass criteria

The agent must notice that materially different formulas are possible, for example:

- `min(actual × percentage, maximum)`;
- `min(actual, maximum) × percentage`.

It must not choose one silently. The calculation semantics must remain **DECISION REQUIRED** until the business defines the formula (and any consequential rounding/precedence rule).

### Fail examples

- Calculates one amount as if the formula were obvious.
- Treats the percentage and maximum as independently meaningful without defining their composition.
- Defers the ambiguity to implementation even though it changes the business reimbursement amount.
