# Temporal Data Review

Temporal correctness is about preserving the business truth that should remain true after mutable reference data changes.

## The three strategies

### 1. Current reference

Historical records intentionally resolve to the latest current value.

Use when the business meaning is identity-based and the latest display value is desired.

Example: a task assigned to employee `E123` may intentionally show the employee's current display name.

### 2. Snapshot

Copy the relevant value at the moment the business event occurs so later changes do not alter historical meaning.

Use when past records must preserve what was true at that time.

Examples:

- order item unit price;
- approved quotation amount;
- shipping address used for a completed shipment;
- standard workhour applied to a completed production record, when required by the business.

A snapshot is intentional duplication. Label it as such.

### 3. Effective-dated history

Persist versions with validity periods such as `effective_from` and `effective_to`, or an equivalent version identity.

Use when the system must answer both:

- what is current now?
- what definition/version was valid at a past point in time?

Examples:

- product versions;
- organizational assignment history;
- contract terms;
- rate tables;
- standard definitions that change over time and remain queryable historically.

## Review procedure

For every mutable business fact ask:

1. Can this value change?
2. If it changes tomorrow, what should yesterday's record display?
3. Does the historical record need the old label only, or the old full definition?
4. Must the system reconstruct the exact state at an arbitrary date?
5. Is the historical requirement legal/audit-critical or merely convenient?

Then classify the fact as current reference, snapshot, or effective-dated history.

## Common failure modes

### Mutable reference pretending to be history

A historical transaction stores only a foreign key to a mutable reference row. Updating the reference silently rewrites what the old transaction appears to mean.

### Snapshot without a source-of-truth definition

The same field appears in several tables, but nobody knows which copy is authoritative or why the duplicates exist.

### Version table without version semantics

A table named `*_version` exists, but old versions are updated in place, making it a naming convention rather than true history.

### Audit timestamps mistaken for history

`updated_at` tells when the current row changed. It does not preserve previous values.

### Soft delete mistaken for temporal modeling

Keeping a deleted row does not explain which definition was effective when a historical event occurred.

## Output expectation

Create a concise temporal decision table for consequential mutable facts:

| Fact | Can change? | Historical expectation | Strategy | Reason |
|---|---|---|---|---|
| Product version | Yes | Production record must preserve the version used | Effective-dated/version identity | Version changes alter business meaning |
| Standard workhour | Yes | Past completed production must keep applied value | Snapshot or version reference | Depends on whether the complete standard definition must be reconstructed |

If the correct strategy depends on unresolved business policy, mark it as a human decision instead of guessing.
