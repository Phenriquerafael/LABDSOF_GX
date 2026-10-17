# Security and Privacy Assessment

**Status:** proposed initial assessment · Sprint 1, deliverable 9 · controls not implemented/verified

## 1. Context and assets

Assets: identity/membership, private text, published summary, location, proposals, schedules, costs, credentials, events and history. Boundaries: client→API, service→service, outbox relay and internal endpoints (no broker proposed, [ADR-003](adr/ADR-003-async-workflow-without-broker.md)), database, AI/calendar provider and research repository.

The MVP uses synthetic demonstration data. Real interviews still require a purpose, participation permission and their own data management. This proposal does not claim legal compliance; legal issues and institutional policies need appropriate review before a real pilot.

## 2. Proposed access matrix

| Operation/data | Resident | Administrator | Contractor |
|---|---|---|---|
| Create report | Buildings they belong to | Managed buildings, with source recorded | Outside baseline |
| Raw report text | Own | Managed buildings | Only necessary extract in the assigned request |
| Published shared summary | Same building | Managed buildings | Context needed for the request |
| Confirmed category/urgency | Authorised viewing | Confirms/corrects | Views assigned request |
| Confirm impact | Same building; own contribution | Views counter | No |
| Proposal/approval | No | Managed buildings | Responds only to assigned request |
| Booking/completion | Authorised public status | Coordinates/validates | Assigned request |
| Cost | No access by default | Managed buildings | Proposes/views cost of own work |
| Close/reopen | No | With reason according to transition | Reports completion, without closing |
| Data from another building/request | No | Only if explicitly authorised | No |

Check in the backend for each operation; hiding buttons is insufficient. The proposed own login ([ADR-005](adr/ADR-005-authentication-authorisation.md); a mock would also be allowed by §6.6) identifies seeded or invited users, and does not allow the client to choose arbitrary privileges. Internal services are also authenticated/authorised.

## 3. Threats and checks

| ID | Threat / impact | Proposed control | Required evidence |
|---|---|---|---|
| T-01 | Change ID to read another building | Membership/resource authorisation and data projection | Negative API/list/search tests |
| T-02 | Contractor approves expenses or reads another request | Permissions and assignment checked on the server | Matrix tests |
| T-03 | Text induces AI to act/disclose | Apply [AI controls](07-responsible-ai-assessment.md#5-controls-and-communication) | [Adversarial cases and protocol](07-responsible-ai-assessment.md#3-proposed-dataset-and-protocol) |
| T-04 | Retry duplicates request/booking | Idempotency, versioning and reconciliation | Failures after external effects and repeated events |
| T-05 | Malicious text in UI | Safe rendering, input validation | Non-executable content tests |
| T-06 | Logs/payloads expose data | Minimisation, sanitisation and restricted access | Log and payload inspection |
| T-07 | Tokens/secrets in Git | Environment injection, review/secret checks | CI and review evidence |
| T-08 | Spam or excessive AI calls | Per-user/input limits, budget and limited retries | Limit and cost tests |
| T-09 | Data loss / component failure | Persistence, backup/restore, degraded states | Recovery exercise |
| T-10 | Changes without accountability | Minimal audit with actor, version and timestamp | Trace an intervention and correction |

Assess likelihood and impact with the team after defining deployment; do not assign a residual risk rating without validating controls.

## 4. Data lifecycle and minimisation

| Data | Proposed collection/access | Retention/deletion |
|---|---|---|
| Demo fixtures | Synthetic, without real people/addresses | Reproducible reset; retain only necessary fixtures |
| Reports/interventions | Minimal text and area reference; restricted private details | Period and rules to be finalised in DEC-07 before a pilot |
| Logs/audit | Necessary metadata, without full text/secrets | Period, access and deletion to be defined with the environment |
| Interviews | Minimal coded notes; optional recording with separate permission | Location, owner and deletion date defined before collection |
| Participant identities | Separate code→identity mapping, if needed | Outside Git; delete when no longer necessary |
| Data sent to a provider | Only what is necessary; synthetic during development | Review provider terms, configuration and retention before use |
| Backups | Restricted access and restore test | Define rotation and the effect of deletion requests |

A participant code is not anonymisation if the person remains identifiable. Do not publish raw notes, contacts, recordings or consent forms in the repository. Recruitment procedure and [consent text](03-user-research.md#consent-text-template) are maintained in the research plan.

## 5. Open questions and review plan

- DEC-07: shared/private area boundary, summary publication, retention, deletion and participant access.
- DEC-04/05/06: identity, secrets and provider configuration.
- Before collecting data: confirm purpose, owner and secure location.
- Before S2: isolation tests and approval workflow review.
- In S3: review threats/implemented controls, dependencies, results and residual risk; record limitations.
- Before a real pilot: review applicable obligations and policies; synthetic data does not prove suitability for real operation.

Traceability: [NFR-01/02/04/09](requirements.md), [backlog](06-product-backlog.md), [research](03-user-research.md).
