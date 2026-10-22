# Initial requirements and quality attributes

**Status:** proposal v0.3 · Sprint 1, Phases 2–3 · reviewed 2026-10-19 (DISC-03-03, EN-01-02) · validation pending

Sources: [assignment](../LABDSOF-26-27-Assignment.md), [hypotheses P1–P7](01-problem-and-opportunity-report.md), [secondary findings PS-01–PS-08](03-user-research.md#72-findings) and [vision J1–J3](04-product-vision.md). No requirements have yet been confirmed through interviews; PS findings support workflow concepts only. “Must” in the backlog is a proposed priority for the academic release, not customer approval.

## Functional requirements

See the [epics table in the Product Backlog](05-product-backlog.md#2-epics) for the scope and items associated with each epic.

| ID | Proposed requirement | Source | Backlog / epic |
|---|---|---|---|
| RF-01 | Record a text report in an authorised building and return a persistent ID | P1, P5 / J1 | US-01 / EP-01 |
| RF-02 | View status and history, triage manually and control transitions | P1, PS-03, PS-05 / J1–J2 | US-02 / EP-01 |
| RF-03 | Suggest category/urgency using AI with provenance, correction and fallback | P3, P7 / §5.6 | US-03 / EP-02 |
| RF-04 | Publish a shared summary and allow impact confirmation/withdrawal without exposing identity; the count never changes urgency | P2, P5 / §5.5, [vision §6](04-product-vision.md#6-ethical-participation-and-visibility) | US-04 / EP-01 |
| RF-05 | Suggest and review links between similar reports while preserving originals | P2 / J2 | US-05 / EP-02 |
| RF-06 | Browse the catalogue, filter eligibility and select a contractor | P1, P6 / J2 | US-06 / EP-03 |
| RF-07 | Approve a request, receive acceptance/rejection/counterproposal and record review | P6, PS-08 / J2–J3 | US-07 / EP-03 |
| RF-08 | Synchronise an external calendar/simulator and record a pending state or manual booking confirmation | P6, PS-07 / §6.5 | US-08 / EP-03 |
| RF-09 | Receive completion reports and let the administrator close/reopen with history | P1 / J2–J3 | US-09 / EP-03 |
| RF-10 | Notify status changes through one channel and respect optional preferences | P5, PS-01, PS-04 / J1–J3 | US-10 / EP-01 |
| RF-11 | Record an optional basic cost with source, currency and audited correction | P1 / J2–J3 | US-11 / EP-03 |

Price/distance selection and embeddings are conditional refinements, not guaranteed available data ([vision §4.4](04-product-vision.md#44-conditions-for-optional-features)). IoT and prediction are excluded from the MVP and from this list (DEC-01, BA/PO proposal of 2026-10-19, team agreement pending). Approval, acceptance, booking confirmation, completion report and closure follow the step definitions in [vision §4.3](04-product-vision.md#43-distinct-steps-dec-10).

## Non-functional requirements / constraints

The numerical targets below are proposals for discussion (DEC-08), not results or production SLAs. Freeze the scenario, environment and thresholds before measuring.

| ID | Quality / scenario | Proposed verification criterion | Items |
|---|---|---|---|
| NFR-01 | Authorisation and isolation | All tests of the author/admin/contractor/other-building matrix deny unauthorised backend access; mock identity does not remove authorisation; Dispatch denies administrator commands when Issue's authorisation context is unavailable ([Technical Design Section: 4.3](07-technical-design.md#43-how-dispatch-obtains-trustworthy-authorisation-context)) | US-01–US-11, EN-03 |
| NFR-02 | Privacy | Synthetic demo data; payloads/logs exclude unnecessary personal fields; retention and deletion defined before real collection | DISC-01, EN-01/03 |
| NFR-03 | AI resilience | Reporting works with AI disabled; proposed timeout of 10 s per attempt; visible fallback/pending state and manual correction available | US-03, EN-03 |
| NFR-04 | Asynchronous delivery | Redelivery of the same event does not duplicate effects; limited retries and observable final failure; test crashes between persistence and publication; starting retry, lease and retention values in [Technical Design Section: 4.6](07-technical-design.md#46-retry-timeout-and-lease-values-proposal) | US-03/08/10, EN-03 |
| NFR-05 | Performance | Proposal: p95 ≤ 2 s for reporting/viewing, 10 concurrent users and 1,000 synthetic issues; exclude external AI time and document hardware | EN-03/04 |
| NFR-06 | Observability | Structured logs, health checks, metrics, correlation ID and traceable errors in a dashboard; demonstrate one complete path | EN-02/03 |
| NFR-07 | Usability/accessibility | Keyboard journeys, visible focus, labels and understandable errors, status not conveyed by colour alone; reporting task in ≤ 2 min as a target to validate | US-01/02, EN-04 |
| NFR-08 | Delivery and maintenance | Reproducible builds, containers, tests/CI, configuration without secrets in Git, instructions and exercised recovery | EN-02/03 |
| NFR-09 | Persistence/recovery | Issues survive restart; backup/restore exercise with counts and integrity checks; RPO/RTO proposed after the environment is known | EN-02/03 |
| NFR-10 | AI quality/cost | Traceable dataset and versions; per-class metrics, latency and cost; budget and limits defined before Ready | US-03, EN-04 |
| NFR-11 | Integration | Failure/delay/incomplete or invalid data produces no false confirmation; repetition does not duplicate bookings; uncertain outcomes are reconciled by request ID before any re-send ([Technical Design Section: 4.5](07-technical-design.md#45-calendarsimulator-contract-outline)) | US-08, EN-03 |

## Priorities, acceptance and traceability

The [backlog](05-product-backlog.md) contains proposed MoSCoW priorities and item-level criteria. A need identified through research must reference E/F in the evidence register and update this table. A significant technical change requires an ADR.

Model, states, transition rules and contract outlines: [Technical Design Sections: 2–4](07-technical-design.md#2-domain-model-and-data-ownership), with examples in the [contract annex](07a-contract-examples.md). Access/data policies: [security](09-security-privacy.md).

## Open decisions

- Technical targets and environment (DEC-08). The primary product threshold is proposed in [vision §5.1](04-product-vision.md#51-primary-success-criterion-dec-08).
- Visible data, retention and ways to request deletion (DEC-07).
- Scheduling and closure business rules (DEC-10); step definitions proposed in vision §4.3 and transition, cancellation and concurrency rules in [Technical Design Section: 3](07-technical-design.md#3-states-and-business-rules) (EN-01-02); rescheduling after a booking confirmation and contractor withdrawal remain open; validation with administrators and contractors pending.
- Support for private reports about individual units in the pilot; the baseline keeps raw reports restricted.
- Assisted access for residents without digital access, to investigate before expanding the MVP.

Reference: [Nonfunctional Requirements — Scaled Agile](https://framework.scaledagile.com/nonfunctional-requirements/), accessed on 2026-10-07. The scenarios and values above are proposals specific to this project.
