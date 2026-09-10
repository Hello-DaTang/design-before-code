# design-readiness-review Evaluation Cases

These evals test whether the reviewer detects contradictions across business, UX, and data artifacts instead of merely summarizing them.

Use `docs/EVALUATION-PROTOCOL.md`: routine edits should run focused unit evals plus a small regression sample.

## Eval 1 — Decision drift across artifacts

### Business artifact

> Whether production records can be edited after submission is DECISION REQUIRED.

### UX artifact

> The detail page has an Edit button for submitted records.

### Data artifact

> `production_record` is mutable and only stores `updated_at`.

### Pass criteria

The reviewer identifies that an unresolved business decision was silently resolved downstream. Outcome cannot be READY. It routes the decision back to business design and notes UX/data elements blocked by it.

---

## Eval 2 — UX promises draft without data support

### Business artifact

> A request may be prepared before final submission; draft behavior is not yet specified.

### UX artifact

> Users can save a draft and resume tomorrow.

### Data artifact

> Only submitted requests exist; all required fields are NOT NULL and there is no draft state.

### Pass criteria

The reviewer flags the UX promise as unsupported and identifies required lifecycle/data implications rather than saying implementation can handle it later.

---

## Eval 3 — Data uniqueness stricter than business rule

### Business artifact

> A product may be produced twice in one shift on different lines; line behavior is still being clarified.

### UX artifact

> Operators can add multiple production entries in the same shift.

### Data artifact

> UNIQUE(production_date, shift, product_id).

### Pass criteria

The reviewer detects that the database constraint contradicts the known/possible business behavior and blocks implementation until the identity/granularity decision is resolved.

---

## Eval 4 — Business lifecycle split collapsed by data

### Business artifact

> Plan is created and approved before execution. Actual execution is recorded later and may happen partially.

### UX artifact

> Planner approves Plan; operator later records Actual.

### Data artifact

> One `production_record` row contains plan_qty, actual_qty, and one status.

### Pass criteria

The reviewer flags likely lifecycle collapse and routes the issue to business/data design. It does not assert a specific final schema automatically.

---

## Eval 5 — Data supports versions but UX hides historical choice

### Business artifact

> Backdated production entry must use the product version effective on the production date.

### UX artifact

> Entry form automatically uses the current version with no visible effective date or version selection/review.

### Data artifact

> Production record stores product_version_id and version history is effective-dated.

### Pass criteria

The reviewer identifies a UX/business mismatch: the data can preserve history, but the user flow can still record the wrong version for backdated work.

---

## Eval 6 — Technical delete conflicts with business voiding

### Business artifact

> Submitted records may be voided but must remain auditable.

### UX artifact

> User clicks “Void” and can later see the voided record/history.

### Data artifact

> The row is only soft-deleted with `deleted=true`; normal queries hide it and there is no void reason/status.

### Pass criteria

The reviewer distinguishes business void from technical soft delete and flags unsupported audit/history semantics.

---

## Eval 7 — Payment retry hidden by single mutable status

### Business artifact

> Payment may fail and the customer may retry. Whether multiple successful payments are allowed is DECISION REQUIRED.

### UX artifact

> Failed payment can be retried and users can view payment attempts.

### Data artifact

> Order contains `payment_status`, `payment_time`, and `transaction_no`; no payment-attempt entity/history.

### Pass criteria

The reviewer identifies that UX promises attempt history the data model cannot represent and that the unresolved multi-payment rule must remain blocked.

---

## Eval 8 — Historical label semantics drift

### Business artifact

> Historical invoices must show the customer address exactly as used when issued.

### UX artifact

> Invoice detail shows “Shipping Address”.

### Data artifact

> Invoice stores customer_address_id only and joins current address fields.

### Pass criteria

The reviewer identifies that current-reference data cannot satisfy the business/UX promise for historical value preservation.

---

## Eval 9 — Missing permission capability

### Business artifact

> Requester can submit; reviewer can approve/reject; requester cannot approve their own request.

### UX artifact

> Detail page shows Approve/Reject based only on generic edit permission.

### Data/API artifact

> Only `request:update` permission exists; reviewer/requester roles are not distinguished for approval.

### Pass criteria

The reviewer flags the missing authorization/business-role distinction and routes it to business/UX/implementation design as appropriate.

---

## Eval 10 — Cross-artifact scenario catches contradiction

### Business artifact

> An order can be partially shipped, then partially refunded for an unshipped item.

### UX artifact

> Order detail allows “Ship selected items” and “Refund selected items”.

### Data artifact

> Order has one shipment_no and one refund_amount at order header level.

### Pass criteria

The reviewer walks through a concrete scenario and shows why header-only shipment/refund data cannot support the promised partial behavior. Outcome is NOT READY or NEEDS DECISION depending on whether partial behavior is confirmed.

---

## Eval 11 — Do not over-block minor details

### Business artifact

> Product can be created and disabled.

### UX artifact

> Product list uses label “Disable”.

### Data artifact

> status values are ACTIVE/INACTIVE.

### Pass criteria

The reviewer does not invent a blocker merely because terminology differs slightly when semantics are aligned. It may note a MINOR naming clarification but can still consider the path supported.

---

## Eval 12 — Missing artifact

### Inputs

Business-domain artifact and data-model artifact exist, but no UX-flow artifact exists for a user-facing module.

### Pass criteria

The reviewer returns NOT_READY by default and explains that user-task feasibility has not been reviewed. It does not infer the UX from CRUD endpoints or database tables.

---

## Eval 13 — Readiness is not human approval

### Inputs

Business, UX, and Data artifacts are fully aligned. No material unresolved decisions remain.

### Pass criteria

The reviewer returns `READY_FOR_HUMAN_APPROVAL`, not “implementation approved” or an automatic handoff to coding. It explicitly states that a human must approve before implementation begins.

---

## Eval 14 — Material upstream change invalidates prior readiness

### Inputs

A previous readiness review passed. Later, business changes the rule from “one shipment per order” to “partial/multiple shipments allowed.” UX and Data artifacts have not yet been updated.

### Pass criteria

The reviewer treats the old readiness result as invalid, re-runs relevant checks, and returns NEEDS_DECISION or NOT_READY until UX/Data are reconciled. It must not rely on the previous READY result.

---

## Eval 15 — Surface/state coverage lens catches an omission

### Business artifact

> Approvers must be able to make a decision even when there are no prior comments.

### UX artifact

> IA lists Request List, Request Detail, Approval Drawer, and Comments panel. Only populated Comments behavior is described.

### Data artifact

> Comments are optional and may be empty.

### Pass criteria

The reviewer uses surface/state coverage to notice that the Comments panel has no empty-state behavior and determines whether that omission blocks/weakens the approval journey. It should find the omission mechanically rather than merely praising overall coherence.

---

## Eval 16 — Project constraint must be re-checked after design

### Project constraint

> Historical financial records must be reproducible exactly as originally issued.

### Business artifact

> Invoice totals are final once issued.

### UX artifact

> Invoice detail shows original line items and totals.

### Data artifact

> Invoice stores product_id and recalculates line price from the current product price.

### Pass criteria

The reviewer identifies a post-design violation of the project constraint and blocks readiness. A pre-design statement that the constraint existed is not enough; the final data semantics must actually satisfy it.
