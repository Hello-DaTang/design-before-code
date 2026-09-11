# Business Model — Internal Application Access Request

## Review status

**Ready for downstream design.** No material business decision remains unresolved for this example.

## 1. Context and decision provenance

**Business goal:** let an employee request internal application access, obtain the required manager decision, and let IT provision only approved requests while preserving decision history.

### FACT

- Employee requests access for themself.
- One request targets one application and one requested access level (`READ` or `EDITOR`).
- Employee's Manager approves or rejects.
- Self-approval is forbidden.
- Rejection is final.
- IT may provision only after approval.
- Employee may cancel before access is granted.
- Cancelled requests cannot later be approved or provisioned.
- IT grants exactly the approved requested level.
- Manager decision actor/time/result and IT provisioning actor/time are preserved.
- Fulfilled requests cannot be cancelled.

### INFERENCE

- Manager decision and IT provisioning are separate business facts with different actors and timing.
- A stale Manager or IT action after cancellation must fail rather than overwrite the cancellation.

### RECOMMENDATION

- Use explicit business states only for explanation; storage does not need to persist a mutable status if it can be derived safely from the business facts.

### ASSUMPTION

- Employee, Manager, and IT identities come from an external corporate identity/directory system.

### DECISION REQUIRED

None.

## 2. Modeling-depth gate

**Light Domain Modeling.** The scope is small, but it contains distinct request, decision, and provisioning lifecycles plus a reachable cancellation/concurrency edge. Plain CRUD would hide those business facts; Deep Domain Discovery would be unnecessary ceremony.

## 3. Actors and goals

| Actor | Goal | Responsibility / authority |
|---|---|---|
| Employee | obtain the access needed for work | submit own request; cancel before fulfillment |
| Employee's Manager | decide whether the business need justifies access | approve or reject; cannot approve own request |
| IT Operator | fulfill an approved access decision | grant the approved level and record provisioning |

## 4. Business terminology

| Term | Meaning | Identity / lifecycle |
|---|---|---|
| Access Request | Employee's request for one application and one access level | durable identity from submission through rejection/cancellation/fulfillment |
| Manager Decision | approval or rejection of one request | immutable decision fact |
| Provisioning Record | fact that IT granted the approved access | immutable fulfillment fact |
| Requested Access Level | `READ` or `EDITOR` requested by Employee and approved unchanged by Manager | value on the request |

Do not confuse Manager approval with actual provisioning: approval authorizes fulfillment; it does not mean access has already been granted.

## 5. Key business events

```text
Employee submits request
    ↓
AccessRequestSubmitted
    ↓
Manager approves or rejects
    ↓
AccessRequestApproved / AccessRequestRejected
    ↓
(if approved and not cancelled)
IT grants approved access
    ↓
AccessProvisioned
```

Alternative event:

```text
Employee cancels before provisioning
    ↓
AccessRequestCancelled
```

Cancellation can happen while Manager review is pending or after approval but before provisioning.

## 6. Business concepts and responsibilities

### Access Request

Represents the Employee's intent to obtain one application/access-level combination. It owns requester, application, requested level, reason, submission time, and cancellation fact.

It does **not** own the Manager decision or provisioning event; those are separate facts with their own actors/timestamps.

### Manager Decision

Represents the Manager's immutable approve/reject decision for one request. It preserves who decided, when, and the result.

### Provisioning Record

Represents the immutable fact that IT granted access after approval. It preserves who provisioned and when. The granted level is the request's approved requested level; IT cannot choose a different value.

## 7. Lifecycle, states, and rules

Conceptual request lifecycle:

```text
SUBMITTED
  ├─ manager rejects → REJECTED (final)
  ├─ employee cancels → CANCELLED (final)
  └─ manager approves → APPROVED
                         ├─ employee cancels → CANCELLED (final)
                         └─ IT provisions → FULFILLED (final)
```

Rules:

1. Manager decision requires an existing non-cancelled `SUBMITTED` request.
2. Manager actor must differ from requester.
3. Rejected is final and cannot be provisioned.
4. Cancellation is allowed only before provisioning and only by the requester.
5. Provisioning requires an approved, non-cancelled request.
6. Provisioned level equals the request's approved requested level.
7. Fulfilled is final for this scope; revocation is outside the model.
8. Manager decision history and provisioning history are never overwritten by a later mutable status field.
9. If two actors act on stale state, only an action whose preconditions still hold may commit; the other action receives the current state and explanation.

## 8. Scenarios

### Normal

1. Elena requests `EDITOR` access to `CRM` for customer-data maintenance.
2. Marcus, Elena's Manager, approves the request.
3. Priya in IT sees the approved request and grants `EDITOR` access.
4. The request is fulfilled; the Manager decision and IT provisioning actor/time remain reviewable.

### Exception — cancellation races with stale review

1. Elena submits a `READ` request.
2. Marcus opens the request in the Manager queue.
3. Before Marcus decides, Elena cancels it.
4. Marcus's stale Approve action is rejected because the request is now final `CANCELLED`.
5. No Manager decision or provisioning record is created from the stale action.

## 9. Model challenges

- Do not collapse Manager Decision into `access_request.status`; actor/time/result history is explicitly required.
- Do not collapse Provisioning into approval; approval authorizes IT but does not prove access was granted.
- Do not add an Application Catalogue entity: catalogue administration is out of scope.
- Do not add revocation/recertification concepts for hypothetical future requirements.
- Do not infer one-request-per-application uniqueness across time; the requirements do not impose it.

## 10. Downstream implications and human review gate

UX must:

- separate Employee, Manager, and IT tasks;
- explain why stale actions fail;
- distinguish approved-but-awaiting-provisioning from fulfilled;
- allow cancellation only while the business preconditions hold.

Data design must:

- preserve Manager Decision and Provisioning as historical facts;
- enforce at most one Manager Decision and at most one Provisioning Record per request;
- avoid an independent stored granted level that could diverge from the approved request value unless a justified snapshot is needed.

**Ready for downstream design.**
