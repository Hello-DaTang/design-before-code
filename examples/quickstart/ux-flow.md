# UX Flow — Internal Application Access Request

## Review status

**Ready for implementation design.** Business semantics are sufficiently resolved for the behavioral UX contract.

## 1. Context and provenance

### FACT

- Employee submits own access request for one application and access level.
- Manager approves/rejects; no self-approval.
- Rejected and cancelled requests are final.
- Employee may cancel before fulfillment.
- IT provisions only approved, non-cancelled requests and grants exactly the approved requested level.

### RECOMMENDATION

- Keep the three jobs in separate task queues rather than one generic admin page.
- Show immutable decision/provisioning history inside request detail rather than hiding it behind logs.

### DECISION REQUIRED

None.

## 2. Actors, jobs, priorities

| Actor | Primary job | Priority |
|---|---|---|
| Employee | request access and understand current outcome | high |
| Manager | review pending requests with enough context to decide | high |
| IT Operator | provision only approved requests | high |

## 3. Information architecture

```text
My Access Requests
  ├─ New Request
  └─ Request Detail

Manager Review Queue
  └─ Request Review

IT Provisioning Queue
  └─ Provisioning Detail
```

No separate Application Catalogue screen is introduced because catalogue administration is out of scope.

## 4. Key journeys

### J1 — Employee submits a request

**Elena, Employee**

Goal: obtain `EDITOR` access to CRM.

1. Open **My Access Requests**.
2. Select **New Request**.
3. Enter application, access level, and business reason.
4. Review the consequence: Manager approval is required before IT can grant access.
5. Submit.
6. See status `Awaiting manager review` and the submitted details.

Validation failure stays on the form and associates errors with the relevant fields.

### J2 — Manager reviews

**Marcus, Manager**

1. Open **Manager Review Queue**.
2. Open Elena's request.
3. See requester, application, requested level, business reason, and submission time before acting.
4. Approve or Reject.
5. On Approve, see confirmation: `Approved — awaiting IT provisioning`.
6. On Reject, see final outcome and decision history.

If Elena cancelled after Marcus loaded the page, the server rejects the stale action and the UI reloads the current `Cancelled` state with an explanation.

### J3 — Employee cancels before fulfillment

1. Elena opens an eligible request in `Awaiting manager review` or `Approved — awaiting IT provisioning`.
2. Select **Cancel request**.
3. Confirmation explains that cancellation is final for this request.
4. Confirm.
5. Request becomes `Cancelled`; approve/provision actions are unavailable.

Cancellation is not shown after fulfillment.

### J4 — IT provisions approved access

**Priya, IT Operator**

1. Open **IT Provisioning Queue**.
2. Open an approved request.
3. See the exact application and approved access level.
4. Grant that level in the external/manual IT process.
5. Select **Mark access granted**.
6. See `Fulfilled`, provisioned-by identity, and time.

The UI does not offer a different grant level.

## 5. Surface inventory

| Surface | Responsibility | Primary action | Important states |
|---|---|---|---|
| My Access Requests | employee's own request history | New Request | loading, empty, populated, error |
| New Request | capture one request | Submit | validation, submitting, failure |
| Request Detail | status + decision/provisioning history | Cancel when eligible | submitted, approved, rejected, cancelled, fulfilled |
| Manager Review Queue | pending Manager work | Open review | loading, empty, failure |
| Request Review | show evidence needed for decision | Approve / Reject | ready, stale/cancelled, submitting, failure |
| IT Provisioning Queue | approved unfulfilled work | Open item | loading, empty, failure |
| Provisioning Detail | exact approved access to fulfill | Mark access granted | ready, stale/cancelled, submitting, failure |

## 6. Interaction and state model

User-facing request states:

```text
Awaiting manager review
Approved — awaiting IT provisioning
Rejected
Cancelled
Fulfilled
```

These are behavioral labels, not a requirement to persist one mutable database status column.

Unavailable actions must explain why:

- `Cancel request` hidden/disabled after `Fulfilled`, `Rejected`, or `Cancelled`.
- Manager actions unavailable once request is cancelled or already decided.
- IT action unavailable until Manager approval and after cancellation/fulfillment.

For stale-state conflicts, never use last-write-wins. The server re-checks business preconditions and returns the current request state.

## 7. Exception and recovery flows

### E1 — Duplicate submit click

Disable repeated submit while the first request is in flight. If the network response is uncertain, reload the employee list before encouraging another submission.

### E2 — Request cancelled while Manager/IT has a stale page open

This state is reachable because cancellation is allowed before fulfillment. A stale Approve or Mark Granted action fails server-side, the current `Cancelled` state is shown, and no conflicting decision/provisioning fact is written.

### E3 — Permission mismatch

If a user opens a queue/action they no longer have permission to use, show read-only/denied behavior and do not expose the action.

### E4 — Server/network failure during consequential action

Keep the evidence/context visible, explain that the outcome is unknown until refreshed, and reload before permitting retry.

## 8. Low-fidelity wireframes

### Employee request detail

```text
CRM access request
--------------------------------
Requested level: EDITOR
Reason: Maintain customer records
Submitted: 2026-09-11 09:20

Status: Approved — awaiting IT provisioning

History
✓ Manager approved — Marcus — 09:42

[Cancel request]
```

### Manager review

```text
Access request review
--------------------------------
Employee: Elena
Application: CRM
Requested level: EDITOR
Reason: Maintain customer records
Submitted: 2026-09-11 09:20

[Reject]                     [Approve]
```

### IT provisioning

```text
Provision approved access
--------------------------------
Employee: Elena
Application: CRM
Approved level: EDITOR
Manager approved: Marcus — 09:42

Grant exactly: EDITOR

[Mark access granted]
```

## 9. Accessibility floor and surface closure

- Critical actions are keyboard reachable.
- Approve/Reject/Cancel/Mark Granted are labeled by text, not color alone.
- Validation/error text is associated with the action/input it explains.
- Status changes are perceivable after submission/decision.
- Every named surface above has loading/error/empty or stale-state behavior where applicable.

Reachability check:

- Manager stale-after-cancel: reachable — cancellation may race with Manager review.
- IT stale-after-cancel: reachable — cancellation may race after approval but before provisioning.
- Cancellation after fulfillment: unreachable by rule, so it is not modeled as a normal recovery path; the action is absent.

## 10. Downstream implications and human review gate

Data/API support must provide:

- one immutable Manager decision per request;
- one immutable provisioning fact per request;
- server-side conditional writes so stale approve/provision actions cannot overwrite cancellation;
- requester ownership/permission checks;
- history needed by Request Detail.

No UX behavior requires drafts, multiple approval stages, alternate granted levels, revocation, or an application catalogue.

**Ready for implementation design.**
