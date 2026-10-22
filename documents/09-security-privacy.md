# Security and Privacy Assessment

**Status:** proposed initial assessment · Sprint 1, deliverable 9 · controls not implemented/verified

## 1. Context and assets

Assets: identity/membership, private text, published summary, location, proposals, schedules, costs, credentials, events and history. Boundaries: client→API, service→service, broker, database, AI/calendar provider and research repository.

The MVP uses synthetic demonstration data. Real interviews still require a purpose, participation permission and their own data management. This proposal does not claim legal compliance; legal issues and institutional policies need appropriate review before a real pilot.

## 2. Proposed access matrix

| Operation/data | Resident | Administrator | Contractor | Enforced in |
|---|---|---|---|---|
| Create report | Buildings they belong to | Managed buildings, with source recorded | Outside baseline | Issue |
| Raw report text | Own | Managed buildings | Only necessary extract in the assigned request | Issue; Dispatch stores only the administrator-written extract |
| Published shared summary | Same building | Managed buildings | Context needed for the request | Issue |
| Confirmed category/urgency | Authorised viewing | Confirms/corrects | Views assigned request | Issue; Dispatch for the contractor's view |
| Confirm impact | Same building; own contribution | Views counter | No | Issue |
| Proposal/approval | No | Managed buildings, one version at a time | Responds only to assigned request | Dispatch, with Issue context |
| Counterproposal | No | Approves or replaces it | Proposes on the assigned request only | Dispatch |
| Booking/completion | Authorised public status | Coordinates/validates | Assigned request | Dispatch |
| Manual booking confirmation | No | Managed buildings, with source and reason | No | Dispatch, with Issue context |
| Rework / cancel intervention | No | Managed buildings, with reason | No | Dispatch, with Issue context |
| Cost | No access by default | Managed buildings | Proposes/views cost of own work | Dispatch |
| Close/cancel/reopen issue | No | With reason according to transition | Reports completion, without closing | Issue |
| Data from another building/request | No | Only if explicitly authorised | No | Both |
| Internal endpoints | No | No | No | Service tokens with the right audience |

Each service enforces the rows it owns. Dispatch takes only the user identity from the token: contractor assignment is checked against Dispatch's own records, and administrator rights over an issue's building are fetched from Issue for every command. If Issue cannot answer, the command fails closed ([design §4.3](07-technical-design.md#43-how-dispatch-obtains-trustworthy-authorisation-context)). Resources outside the caller's scope answer `404`, not `403`, to avoid enumeration.

Check in the backend for each operation; hiding buttons is insufficient. Mock authentication identifies predefined test users, but does not allow the client to choose arbitrary privileges. Internal services are also authenticated/authorised.

## 3. Threats and checks

| ID | Threat / impact | Proposed control | Required evidence |
|---|---|---|---|
| T-01 | Change ID to read another building | Membership/resource authorisation and data projection | Negative API/list/search tests |
| T-02 | Contractor approves expenses or reads another request | Permissions and assignment checked on the server | Matrix tests |
| T-03 | Text induces AI to act/disclose | Apply [AI controls](06-responsible-ai-assessment.md#5-controls-and-communication) | [Adversarial cases and protocol](06-responsible-ai-assessment.md#3-proposed-dataset-and-protocol) |
| T-04 | Retry duplicates request/booking | Idempotency, versioning and reconciliation ([design §3.3, §4.5](07-technical-design.md#33-invalid-and-concurrent-transitions)) | Failures after external effects and repeated events |
| T-05 | Malicious text in UI | Safe rendering, input validation | Non-executable content tests |
| T-06 | Logs/payloads expose data | Minimisation, sanitisation and restricted access | Log and payload inspection |
| T-07 | Tokens/secrets in Git | Environment injection, review/secret checks | CI and review evidence |
| T-08 | Spam or excessive AI calls | Per-user/input limits, budget and limited retries | Limit and cost tests |
| T-09 | Data loss / component failure | Persistence, backup/restore, degraded states | Recovery exercise |
| T-10 | Changes without accountability | Minimal audit with actor, version and timestamp | Trace an intervention and correction |
| T-11 | Forged or stale authorisation context in Dispatch (client-supplied building or role, membership revoked after a check) | Context from Issue's internal API for each administrator command; no client-supplied authority; read-only cache of at most 30 s; fail closed | Negative cases W5–W7 in the [contract annex](07a-contract-examples.md#3-review-walkthrough) |

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

Traceability: [NFR-01/02/04/09](requirements.md), [backlog](05-product-backlog.md), [research](03-user-research.md).
