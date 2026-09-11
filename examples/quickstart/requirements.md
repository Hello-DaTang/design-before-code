# Requirements — Internal Application Access Request

Build a small internal workflow for requesting access to company applications.

## Actors

- **Employee** — requests access for themself.
- **Employee's Manager** — approves or rejects the request.
- **IT Operator** — grants access after approval.

## Requirements

1. An Employee submits a request for one internal application.
2. A request contains:
   - application name/code;
   - requested access level: `READ` or `EDITOR`;
   - business reason;
   - submission time.
3. The Employee's Manager approves or rejects the request.
4. Self-approval is not allowed.
5. A rejected request is final and must not be provisioned.
6. An approved request becomes eligible for IT provisioning.
7. The Employee may cancel a request before access has been granted.
8. A cancelled request must not be approved or provisioned afterward.
9. IT grants exactly the access level approved on the request.
10. Preserve the Manager decision actor, time, and result.
11. Preserve the IT provisioning actor and time.
12. Once access has been granted, the request is fulfilled and cannot be cancelled.

## Out of scope

- automatic application provisioning;
- SSO / identity-provider integration;
- access revocation after fulfillment;
- periodic access review / recertification;
- delegation or proxy requests;
- multi-stage approval chains;
- temporary-expiry access;
- application catalogue administration.

## Human-owned decisions already resolved for this example

- One request targets exactly one application and one access level.
- Manager approval applies to that exact requested access level; IT cannot substitute another level.
- Cancellation is allowed while the request is still pending Manager review or approved-but-not-yet-provisioned.
- If Manager/IT has a stale screen after cancellation, the server rejects the action and returns the current final state.
