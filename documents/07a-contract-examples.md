# Technical Design — Annex A: contract examples

**Status:** illustrative proposal · Sprint 1, deliverable 7 annex · EN-01-02 ([Issue: 20](https://github.com/Phenriquerafael/LABDSOF_GX/issues/20)), 2026-10-19 · not an executable contract

Representative requests, responses, events and errors for the principal journey (J2, with J1 and J3 where they meet it). The rules are in the [Technical Design Sections: 2–4](07-technical-design.md#2-domain-model-and-data-ownership); if this annex and the design disagree, the design wins and this annex must be corrected. All data is synthetic. IDs are UUIDv7-shaped with a readable suffix (`…0101` resident, `…0201` administrator, `…0301` contractor account, `…0401` contractor, `…b001` building). Paths are relative to each service's base path, and routing is left to EN-01-03. Headers are shown only where they matter.

## 1. Principal journey

### 1.1 Resident submits a report (Issue, J1)

```http
POST /reports
Idempotency-Key: 4b8e6f0c-2d1a-4c55-9a77-3f0e1d2c9a10
X-Correlation-Id: c-7f3a

{ "buildingId": "0199f2a0-0000-7000-8000-00000000b001",
  "areaId": "0199f2a0-0000-7000-8000-00000000a001",
  "text": "Water dripping from the ceiling light in the garage, level -1." }
```

```http
201 Created
ETag: "1"

{ "reportId": "0199f2a1-0000-7000-8000-000000001001",
  "issueId": "0199f2a1-0000-7000-8000-000000002001",
  "state": "received", "version": 1,
  "submittedAt": "2026-11-03T09:12:04Z" }
```

The report, issue, triage job and `ReportSubmitted` outbox row are committed together. Repeating the request with the same key returns this same body and creates nothing.

### 1.2 Administrator records the triage decision (Issue)

The worker has stored a suggestion (`in_triage`, version 2). The administrator confirms it with a corrected urgency:

```http
POST /issues/0199f2a1-0000-7000-8000-000000002001/triage
If-Match: "2"
Idempotency-Key: 9d0c…

{ "category": "plumbing", "urgency": "high",
  "reason": "Water near an electrical fitting" }
```

```http
200 OK
ETag: "3"

{ "issueId": "0199f2a1-0000-7000-8000-000000002001",
  "state": "triaged", "version": 3,
  "category": "plumbing", "urgency": "high", "decisionSource": "human" }
```

### 1.3 Administrator creates and approves an intervention (Dispatch)

```http
GET /contractors?issueId=0199f2a1-0000-7000-8000-000000002001

200 OK
{ "items": [
    { "contractorId": "0199f2a0-0000-7000-8000-000000000401",
      "displayName": "Synthetic Plumbing Co.",
      "matched": ["specialisation:plumbing", "coverage:building"],
      "unknown": ["price", "distance"] } ] }
```

```http
POST /interventions
Idempotency-Key: 1f2e…

{ "issueId": "0199f2a1-0000-7000-8000-000000002001",
  "contractorId": "0199f2a0-0000-7000-8000-000000000401",
  "scopeExtract": "Leak above garage light fitting, level -1. Access via management.",
  "window": { "start": "2026-11-05T09:00:00Z", "end": "2026-11-05T12:00:00Z" },
  "terms": "Inspect and repair; report cost if known." }
```

Before writing, Dispatch asks Issue for the authorisation context ([Techical Design Section: 4.3](07-technical-design.md#43-how-dispatch-obtains-trustworthy-authorisation-context)):

```http
GET /internal/issues/0199f2a1-0000-7000-8000-000000002001/dispatch-context?actorId=0199f2a0-0000-7000-8000-000000000201
Authorization: Bearer <service token, aud=issue, sub=dispatch>

200 OK
{ "issueId": "0199f2a1-0000-7000-8000-000000002001",
  "buildingId": "0199f2a0-0000-7000-8000-00000000b001",
  "state": "triaged", "version": 3,
  "category": "plumbing", "urgency": "high",
  "areaLabel": "Garage level -1",
  "actorRoles": ["administrator"], "dispatchable": true }
```

```http
201 Created
ETag: "1"

{ "interventionId": "0199f2a2-0000-7000-8000-000000003001",
  "state": "awaiting_approval", "version": 1, "proposalVersion": 1 }
```

```http
POST /interventions/0199f2a2-0000-7000-8000-000000003001/approval
If-Match: "1"
Idempotency-Key: 77aa…

{ "proposalVersion": 1 }

200 OK
ETag: "2"
{ "state": "sent", "version": 2, "approvedProposalVersion": 1 }
```

Event delivered by the Dispatch relay to `POST /internal/events` on Issue, after the approval has committed:

```json
{ "eventId": "0199f2a2-0000-7000-8000-000000005001",
  "eventType": "InterventionApproved",
  "schemaVersion": 1,
  "aggregateId": "0199f2a2-0000-7000-8000-000000003001",
  "aggregateVersion": 2,
  "buildingId": "0199f2a0-0000-7000-8000-00000000b001",
  "occurredAt": "2026-11-03T10:02:11Z",
  "correlationId": "c-81b0",
  "payload": { "issueId": "0199f2a1-0000-7000-8000-000000002001",
               "contractorDisplayName": "Synthetic Plumbing Co.",
               "approvedProposalVersion": 1,
               "interventionState": "sent" } }
```

Issue checks that `issueId` belongs to `buildingId`, records the `eventId` and moves the issue to `in_coordination` in one transaction.

### 1.4 Contractor accepts (Dispatch, J3)

```http
POST /interventions/0199f2a2-0000-7000-8000-000000003001/response
If-Match: "2"
Idempotency-Key: 5c3d…

{ "proposalVersion": 1, "decision": "accept" }

200 OK
ETag: "3"
{ "state": "scheduling_pending", "version": 3 }
```

Dispatch authorises this from its own data: the intervention's contractor `…0401` has `userId` `…0301`, which equals the token's `sub`.

### 1.5 Booking with a timeout and reconciliation (Dispatch ↔ calendar simulator)

```http
POST /bookings
Idempotency-Key: 0199f2a2-0000-7000-8000-000000004001

{ "requestId": "0199f2a2-0000-7000-8000-000000004001",
  "contractorRef": "SIM-CTR-17",
  "window": { "start": "2026-11-05T09:00:00Z", "end": "2026-11-05T12:00:00Z" },
  "buildingCode": "B-001",
  "summary": "Plumbing intervention" }
```

No answer within 5 s, so the booking becomes `uncertain`. Before any re-send, Dispatch reconciles:

```http
GET /bookings?requestId=0199f2a2-0000-7000-8000-000000004001

200 OK
{ "items": [ { "requestId": "0199f2a2-0000-7000-8000-000000004001",
               "externalRef": "SIM-BK-55821", "status": "confirmed",
               "window": { "start": "2026-11-05T09:00:00Z", "end": "2026-11-05T12:00:00Z" } } ] }
```

The booking was created before the timeout. Dispatch stores `externalRef`, marks the booking `confirmed` (source `calendar`), moves the intervention to `booked` and emits `BookingConfirmed`. Nothing is re-sent, so there is one booking.

```json
{ "eventId": "0199f2a2-0000-7000-8000-000000005002",
  "eventType": "BookingConfirmed", "schemaVersion": 1,
  "aggregateId": "0199f2a2-0000-7000-8000-000000003001", "aggregateVersion": 4,
  "buildingId": "0199f2a0-0000-7000-8000-00000000b001",
  "occurredAt": "2026-11-03T10:05:40Z", "correlationId": "c-81b0",
  "payload": { "issueId": "0199f2a1-0000-7000-8000-000000002001",
               "window": { "start": "2026-11-05T09:00:00Z", "end": "2026-11-05T12:00:00Z" },
               "source": "calendar", "interventionState": "booked" } }
```

### 1.6 Completion and closure

```http
POST /interventions/0199f2a2-0000-7000-8000-000000003001/completion
If-Match: "4"
Idempotency-Key: 8e1b…

{ "notes": "Replaced cracked pipe joint; light fitting dried and tested.",
  "proposedCost": { "amount": "85.00", "currency": "EUR" } }

200 OK
ETag: "5"
{ "state": "completion_reported", "version": 5 }
```

After `CompletionReported` reaches Issue (issue `awaiting_validation`, version 6):

```http
POST /issues/0199f2a1-0000-7000-8000-000000002001/closure
If-Match: "6"
Idempotency-Key: 2a9f…

{ "kind": "validated", "reason": "Completion checked with the building caretaker" }

200 OK
ETag: "7"
{ "state": "resolved", "version": 7, "resolution": { "kind": "validated" } }
```

Issue emits `IssueResolved`. Dispatch moves the intervention to `closed`. Residents see the public history entries (received, triaged, scheduled for 5 Nov, resolved), but not the cost, contractor notes or internal reasons.

## 2. Error examples

Stale version (another administrator approved first):

```http
409 Conflict
Content-Type: application/problem+json

{ "type": "https://bmc.example/problems/version-conflict",
  "title": "The resource changed since you loaded it",
  "status": 409, "code": "VERSION_CONFLICT",
  "currentVersion": 2, "currentState": "sent",
  "correlationId": "c-81b4" }
```

Resident of another building reads an issue: the answer does not reveal that the issue exists.

```http
404 Not Found
Content-Type: application/problem+json

{ "type": "https://bmc.example/problems/not-found",
  "title": "Not found", "status": 404, "code": "NOT_FOUND",
  "correlationId": "c-90aa" }
```

Issue is unavailable when an administrator approves: Dispatch fails closed.

```http
503 Service Unavailable
Content-Type: application/problem+json
Retry-After: 5

{ "type": "https://bmc.example/problems/dependency-unavailable",
  "title": "Authorisation could not be checked; nothing was changed",
  "status": 503, "code": "DEPENDENCY_UNAVAILABLE",
  "correlationId": "c-91c2" }
```

Idempotency key reused with a different body:

```http
422 Unprocessable Content
Content-Type: application/problem+json

{ "type": "https://bmc.example/problems/idempotency-key-reused",
  "title": "This key was already used for a different request",
  "status": 422, "code": "IDEMPOTENCY_KEY_REUSED",
  "correlationId": "c-92d0" }
```

`bmc.example` is a placeholder domain for problem types. The final type URIs are set with the OpenAPI.

## 3. Review walkthrough

The EN-01-02 verification asks the reviewer to walk one successful journey and the failure and denied-access scenarios through the model and contracts. Each row names the expected outcome and the rule that produces it.

| # | Scenario | Expected outcome | Rule |
|---|---|---|---|
| W1 | 1.1–1.6 end to end | Issue `received` → `triaged` → `in_coordination` → `scheduled` → `awaiting_validation` → `resolved`; intervention `awaiting_approval` → `sent` → `scheduling_pending` → `booked` → `completion_reported` → `closed`; one booking | Design 3.1, 3.2 |
| W2 | AI times out during triage | Issue stays `received` or shows fallback; administrator triages manually; a late suggestion does not overwrite the decision | 3.1, 3.3, NFR-03 |
| W3 | Resident of building B reads or confirms impact on an issue in building A | `404`; no count change | 4.1, [access matrix](09-security-privacy.md#2-proposed-access-matrix), T-01 |
| W4 | Contractor calls approval, or responds to an intervention not assigned to them | `403` for approval; `404` for the unassigned intervention | 4.3 step 2, T-02 |
| W5 | Client sends a `buildingId` it administers with an issue from another building | Ignored; Dispatch uses the Issue context, where `actorRoles` is empty, so `404` | 4.3 steps 1 and 3 |
| W6 | Administrator membership revoked after the intervention was created | The next administrator command on it returns `404` | 4.3 step 4 |
| W7 | Issue down during approval | `503` `DEPENDENCY_UNAVAILABLE`; nothing changes; contractor responses on other interventions still work | 4.3 steps 2 and 6 |
| W8 | Approval sent twice (double click, same key), and by two administrators (different keys) | Same key: same result. Different keys: one `200` and one `409`. One request is sent in both cases | 3.3 |
| W9 | Contractor counterproposes | New version, `awaiting_approval`; approving it moves to `scheduling_pending` without a second acceptance | 3.2 |
| W10 | Calendar returns a conflict | Booking `rejected`; intervention stays `scheduling_pending` with the reason; manual booking confirmation possible and shown as manual | 4.5 |
| W11 | Calendar timeout after creation (1.5), and repeated delivery of `BookingConfirmed` to Issue | One booking; the duplicate `eventId` is acknowledged without effect | 4.5, 3.3, NFR-11 |
| W12 | Simulator restarts and loses the booking | Reconciliation marks it `discrepancy` for the administrator; it is not silently recreated | 4.5 |
| W13 | Administrator closes the issue manually while the intervention is `booked` | Issue `resolved` (manual); intervention `cancellation_pending` until the external cancellation succeeds, then `cancelled` | 3.1, 3.2 |
| W14 | Validated closure attempted before `CompletionReported` arrives | `409` `INVALID_TRANSITION`; the UI shows the last update time | 3.3 |
| W15 | Reopening a `resolved` issue, and a `cancelled` one | `resolved`: `in_triage` with reason, earlier intervention stays `closed`. `cancelled`: `409` | 3.1 |
| W16 | Dispatch relay down for 10 minutes | Events stay as pending outbox rows and are delivered later; Issue shows the last update time; after 10 attempts a row is `failed` and visible | 4.6, NFR-04 |

Record the reviewer, date and any rule changes in the pull request and in the [EN-01-02 completion record](backlog/tasks/EN-01-02-define-domain-and-contracts.md#completion-record).
