READINESS: READY_FOR_HUMAN_APPROVAL

# Design Readiness Review — Internal Application Access Request

## Artifact inventory

- `requirements.md` — complete.
- `business-model.md` — complete; no unresolved material decision.
- `ux-flow.md` — complete; Employee / Manager / IT journeys and reachable stale-state recovery specified.
- `data-model.md` — complete; identity, cardinality, history, and conditional-write implications represented.

## Decision reconciliation

| Decision / rule | Business | UX | Data | Result |
|---|---|---|---|---|
| one request = one application + one requested level | explicit FACT | form and review show one pair | stored on `access_request` | CONSISTENT |
| Manager approve/reject, no self-approval | lifecycle/rule | Manager review journey | immutable `manager_decision`; actor inequality guard | CONSISTENT |
| rejection is final | explicit rule | no later provisioning action | provisioning precondition requires approval | CONSISTENT |
| Employee may cancel before fulfillment | explicit rule | cancellation offered in reachable pre-fulfillment states | cancellation facts + transactional guard | CONSISTENT |
| cancelled request cannot later be approved/provisioned | explicit rule | stale actions fail and reload current state | conditional write/precondition checks | CONSISTENT |
| IT grants exactly approved requested level | explicit rule | provisioning surface exposes no alternate level | no duplicate `granted_level`; request value is authoritative | CONSISTENT |
| preserve Manager decision history | explicit requirement | shown in detail/history | immutable decision row | CONSISTENT |
| preserve provisioning actor/time | explicit requirement | shown after fulfillment | immutable provisioning row | CONSISTENT |

## Lifecycle / reachability review

Business and UX agree on:

```text
SUBMITTED
  ├─ REJECTED
  ├─ CANCELLED
  └─ APPROVED
       ├─ CANCELLED
       └─ FULFILLED
```

The stale-state path is reachable and intentionally retained:

- Manager can have an open review while Employee cancels a still-pending request.
- IT can have an approved item open while Employee cancels before provisioning.

Cancellation after fulfillment is not reachable by rule and is not presented as a normal recovery flow.

## Task-to-data traceability

| UX task | Data capability | Result |
|---|---|---|
| submit request | `access_request` | SUPPORTED |
| Manager approve/reject | unique immutable `manager_decision` | SUPPORTED |
| Employee cancel pre-fulfillment | cancellation facts + server precondition | SUPPORTED |
| IT mark access granted | unique immutable `provisioning_record` | SUPPORTED |
| show request history | request + decision + provisioning facts | SUPPORTED |
| reject stale action after cancellation | transactional/precondition guard | SUPPORTED |

## Data-to-business justification

All three modeled tables correspond to durable business meaning:

- request intent;
- Manager decision;
- provisioning fact.

The data model does not introduce an application catalogue, revocation, recertification, multi-stage approval, generic audit table, or other out-of-scope concept.

The absence of a stored granted-level value is consistent with the business rule that IT must grant exactly the request's approved level; storing a second independent value would create unnecessary contradiction risk.

## Findings

### Blocking

None.

### Non-blocking

- The example intentionally leaves application/directory integration outside the model; ids/codes are opaque external references.
- Exact database-specific concurrency implementation belongs to technical design. The behavioral/data contract requires conditional writes and no last-write-wins, but does not mandate one DB mechanism here.

## Gate summary

**READY_FOR_HUMAN_APPROVAL.**

Business, UX, and Data describe the same scoped system. No unresolved consequential decision, lifecycle contradiction, unsupported UX promise, unjustified data concept, or silent product choice remains in the example.

This readiness result is not human approval and does not authorize implementation by itself.
