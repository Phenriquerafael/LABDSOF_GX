# ADR-001 — Backend boundaries and deployment

**Status:** Proposed · **Proposal date:** 2026-10-07 · **Revised:** 2026-10-15 (EN-01-01) · **Proposer:** Ricardo (P2, Tech Lead) · **Decision-makers:** team review pending · **DEC:** DEC-04

## Context

§6.2 requires at least two independently deployable backend components; a modular alternative depends on justification and acceptance. BMC distinguishes issue/triage from intervention preparation and execution. EN-01-01 ([#19](https://github.com/Phenriquerafael/LABDSOF_GX/issues/19)) must decide boundaries, ownership, worker placement and integration approach and record the consequences of shared infrastructure.

## Alternatives

| Alternative | Expected advantages | Costs/risks | Evidence |
|---|---|---|---|
| A. Issue + Dispatch, with asynchronous triage worker | Explicit domain boundaries and external failures; separate deployment | Eventual consistency, contracts, tests and distributed operations | E31 §6.2; [design §1](../08-technical-design.md#1-context-and-boundaries) |
| B. Domain backend + independent worker | Fewer data boundaries; AI isolation | Justify worker autonomy/responsibility and suitability with lecturers | E31 §6.2 |
| C. Modular monolith | Lower initial operational and transaction costs | Exception requires evidence and acceptance; modules must have verifiable boundaries | E31 §6.2 |
| D. Service per role/subscription | Extensive separation | No demonstrated need; excessive cost for the MVP | — |

## Proposed decision (not accepted)

**A.** No modular-monolith exception is requested, so no lecturer acceptance is needed or claimed.

| Unit | Owns | Data access |
|---|---|---|
| Issue service | Users and credentials ([ADR-005](ADR-005-authentication-authorisation.md)), organisations, buildings, memberships, reports, issues, triage suggestions, summaries, impact confirmations, audit, in-app notifications, triage job queue | Only writer of database `issue` |
| Dispatch service | Contractors, interventions, proposals and versions, responses, bookings, cost records, calendar adapter | Only writer of database `dispatch` |
| Triage worker | No data of its own; claims jobs and submits versioned results through Issue's internal API; calls the AI provider through an adapter with timeout and rules fallback | No database credentials |
| Calendar simulator | Simulated external bookings ([ADR-006](ADR-006-calendar-simulator.md)) | In-memory; not a business component |

- **Persistence:** one MySQL instance, one database and one database user per service, migrations owned by each service, no cross-database queries or writes ([ADR-002](ADR-002-technology-stack.md)).
- **Communication:** synchronous HTTP for requests and queries that need an immediate answer (for example, Dispatch validating an issue reference); asynchronous outbox delivery for triage, state propagation and notifications ([ADR-003](ADR-003-async-workflow-without-broker.md)). IDs and versioned contracts; no broker in the MVP.
- **Notifications:** an in-app notification module inside the Issue service consumes state events; a separate notification process is not created for the MVP. EN-01-03 may revisit placement.
- **AI provider:** the worker's adapter keeps the provider replaceable. The Gemini Flash family is the candidate named by the team; the model, tier and evaluation remain DEC-05/AI-01 decisions.

## Consequences and validation

- Issue, Dispatch, the worker and the simulator have their own builds, images, configuration and health checks; Issue and Dispatch also own migrations.
- Shared infrastructure: the single MySQL instance and the Docker host are shared dependencies and single points of failure. Mitigations: separate database users and grants, health/readiness checks, restart policies, backup/restore exercise, compatible migrations so each service deploys independently.
- The worker depends on Issue's internal API being available; claimed jobs time out and return to the queue if the worker dies.
- Eventual consistency is visible: issue history may reflect Dispatch events with a delay ([design §3](../08-technical-design.md#3-states-and-business-rules)).
- Tests must cover worker unavailability, redelivery, out-of-order events and delayed updates (NFR-04).
- Effort is higher than option C; accepted in exchange for meeting §6.2 without an exception. Re-estimate at Sprint B planning.

## Open questions

Service-to-service key handling details (EN-01-04), cancellation/reconciliation detail (DEC-10, EN-01-03), notification placement after the MVP (DEC-06). Remaining items with owners are in the [register](../planning/decisions-and-feedback.md#5-en-01-01-unresolved-details).

## Record

- 2026-10-07: first proposal.
- 2026-10-15: revised by Ricardo (Tech Lead) in a structured design interview for EN-01-01 (drafting and source checks AI-assisted; sources in the [evidence register](README.md#evidence-register-en-01-01)). Evidence: issue [#19](https://github.com/Phenriquerafael/LABDSOF_GX/issues/19) and the EN-01-01 pull request. Affected documents: [technical design](../08-technical-design.md), [ADR index](README.md), DEC-04/06.
- **Final decision / date / participants:** pending team review.
