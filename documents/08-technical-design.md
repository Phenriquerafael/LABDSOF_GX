# Technical Design

**Status:** proposed conceptual design · Sprint 1, deliverable 7 · stack proposed in ADR-001–006 (EN-01-01, 2026-10-15; team review pending) · executable contracts pending

## 1. Context and boundaries

Residents, administrators and contractors use a proposed responsive web client (React + Vite SPA, installable PWA shell, Tailwind). The backend coordinates issues and interventions, calls an AI provider and integrates an external calendar simulator. The proposed stack is in [ADR-002](adr/ADR-002-technology-stack.md) (DEC-04); it is not implemented.

| Proposed unit | Responsibility / data it controls | Communication |
|---|---|---|
| Client | Forms, queues, states and corrections; no authority over permissions | Authenticated API through the nginx single origin |
| Issue Service | Users/credentials, buildings/memberships for authorisation, reports, issues, triage jobs and suggestions, summaries, confirmations, audit and in-app notifications | API; outbox events; internal triage-job API |
| Dispatch Service | Catalogue, proposals, responses, bookings and costs | API; outbox events; calendar adapter |
| Triage worker | Claims triage jobs, calls AI/rules, delivers versioned suggestions | Internal Issue Service API only; no database credentials |
| Notification module | Delivers idempotent in-app notifications; proposed as a module of the Issue Service for the MVP | State events; in-app channel (DEC-06) |
| Outbox and relay | Transport and redelivery without a broker ([ADR-003](adr/ADR-003-async-workflow-without-broker.md)) | Outbox/job tables in the owner's database; authenticated internal endpoints |
| Storage | MySQL 9.7: one instance, databases `issue` and `dispatch` | One database user per service; no cross-database access |
| Calendar simulator | Booking creation/query responses and simulatable failures; in-memory state ([ADR-006](adr/ADR-006-calendar-simulator.md)) | External contract with reference and idempotency key |

Issue and Dispatch are the **two proposed independently deployable backend components** ([ADR-001](adr/ADR-001-backend-boundaries.md)); the triage worker and the simulator run as their own containers. No modular-monolith exception is requested. Do not use a simulator as an artificial substitute for a business component.

One MySQL instance hosts a separate database per service in the academic environment; this does not authorise cross-writing. The shared instance and host are operational dependencies and single points of failure; migrations stay compatible so each service deploys independently. Service-to-service authentication is proposed in [ADR-005](adr/ADR-005-authentication-authorisation.md).

## 2. Domain model

| Entity | Conceptual relationships and fields |
|---|---|
| Organisation | Manages several buildings; can represent a property management company |
| Building | Belongs to an organisation; areas/locations; authorisation identifier |
| User / Membership | A user may have a role in several buildings; a global role does not grant universal access |
| Report | Author, building, area, private text, timestamp and source; linked to an issue |
| Issue | Aggregates one or more reports; category/urgency, state, version, optional published summary |
| Triage suggestion | Issue, source version, result, rules/model/prompt and timestamp |
| Impact confirmation | Unique user + issue; withdrawal possible |
| Contractor | Specialisation/coverage; synthetic data in the MVP |
| Intervention | Referenced issue, contractor, proposal and version, approval and response |
| Booking | Intervention, interval, state, external reference or manual source |
| Cost record | Intervention, optional amount, currency, source and confirmation |
| Audit event | Actor, action, reference, version and timestamp; avoid unnecessary private text |
| Notification | Unique event/recipient/channel, delivery state |

Cardinalities: organisation 1:N buildings; users N:M buildings through memberships; issue 1:N reports and 0:N interventions; intervention 0:N booking versions, with at most one active. Contracts use IDs; neither service directly modifies the other's data.

## 3. States and business rules

| Issue progression | Proposed rule |
|---|---|
| Received → in triage → triaged | Reporting independent of AI; administrator confirms triage |
| Triaged → in coordination | Intervention request being prepared/sent |
| In coordination → scheduled | Projection of an actually confirmed booking |
| Scheduled → in progress → awaiting validation | Assigned contractor reports progress/completion |
| Awaiting validation → resolved | Administrator validates; returning to execution requires a reason |
| Resolved → in triage | Reopening with a reason |
| Open → manually resolved/cancelled | Administrator justifies; coordinate cancellation of any active intervention |

Interventions have their own state: proposed → approved/sent → accepted or declined/counterproposed → scheduling pending → externally/manually confirmed → in progress → completed. A counterproposal changes the version and requires new approval. Cancellations propagate with a visible pending state until reconciliation.

Issue history may reflect Dispatch events with a delay; show the update timestamp. Do not confuse eventual consistency with confirmation of an action that did not happen. DEC-10 validates these rules before implementation.

## 4. Initial API (no executable contract yet)

The [access matrix](09-security-privacy.md#2-proposed-access-matrix) defines policy; the table below shows its application to endpoints. Functional criteria remain in the [backlog](06-product-backlog.md#4-stories-and-criteria).

| Proposed operation | Authorisation / result |
|---|---|
| POST /reports | Building resident; returns ID after persistence; idempotency key |
| GET /issues and GET /issues/{id} | Membership filter and role-specific projection |
| POST /issues/{id}/triage | Building administrator; version and reason |
| POST /issues/{id}/publication | Administrator publishes a sanitised summary |
| PUT/DELETE /issues/{id}/impact | Building resident; unique contribution |
| POST /issues/{id}/associations | Administrator; audited linking/reversal |
| GET /contractors | Administrator; explicit filters |
| POST /interventions | Administrator; proposal with authorised issue |
| POST /interventions/{id}/approval | Administrator; version and idempotency |
| POST /interventions/{id}/response | Assigned contractor; accept/decline/counterpropose |
| POST /interventions/{id}/manual-confirmation | Administrator; source, interval and reason |
| POST /interventions/{id}/completion | Assigned contractor or administrator with a reason |
| POST /issues/{id}/closure or /reopening | Administrator; state and version validation |

Define OpenAPI contracts/schemas after stack selection: 400/401/403 errors or 404 without enumeration, 409 for version/conflict, 503 for material unavailability. Do not return persistence success if the database fails.

## 5. Asynchronous workflow and consistency

1. Issue persists the report/issue, a triage job and an outbox record in the same transaction.
2. The triage worker claims the job through Issue's internal API (Issue uses `SELECT … FOR UPDATE SKIP LOCKED` with a lease); a worker failure returns the job to the queue after the lease expires.
3. The worker validates schema, ID and version and applies the [AI/baseline/fallback workflow](07-responsible-ai-assessment.md#2-baseline-and-fallback), with the timeout defined in NFR-03.
4. The result reaches Issue's authorised internal interface; an old result does not replace a human correction.
5. Change events feed projections/notifications; a relay delivers outbox rows to the other service's internal endpoint; receivers deduplicate by eventId in the same transaction as the effect.
6. Dispatch manages approval/acceptance and issues a calendar request only when eligible.

Proposed envelope: eventId, eventType, schemaVersion, aggregateId, aggregateVersion, buildingId, occurredAt, correlationId and minimal payload. Received building identifiers are also validated; they do not suffice as authorisation.

No broker is used in the MVP ([ADR-003](adr/ADR-003-async-workflow-without-broker.md)). Retries use increasing delays and a configured limit; exhausted attempts mark the row `failed` (dead-letter equivalent) with controlled replay. Test crashes after persistence and before publication, repeated delivery and out-of-order events. Do not promise “exactly once” delivery; build idempotent effects.

## 6. External integration and degraded mode

The calendar/simulator must support requests with stable IDs, query/reconciliation and scenarios: success, timeout, unavailability, delay, incomplete/invalid response and scheduling conflict.

Timeout may occur after the external event is created; query/reconcile before retrying. Manual confirmation remains distinct from external synchronisation and suspends duplicate automatic creation until reconciliation. A calendar slot alone does not prove contractor availability.

AI unavailable: rules + manual review. Worker or relay unavailable: persisted report, job or outbox row stays pending; manual triage remains possible. Calendar unavailable: pending/manual. Notification failure: status remains queryable. Database unavailable: explicit error without false acknowledgement.

## 7. Proposed deployment and operations

- Containers ([ADR-004](adr/ADR-004-deployment-contract.md)): nginx client/reverse proxy, Issue, Dispatch, triage worker, calendar simulator and MySQL; optional `observability` profile. Images for amd64 and arm64; one `compose.yaml`; any Docker host.
- Expose only nginx; database and internal endpoints on an internal network.
- Configuration per environment; secrets injected outside Git; least-privilege service identities.
- GitHub Actions builds, tests (with a MySQL 9.7 service container) and publishes images to GHCR; deployment is `docker compose pull && up -d`; demonstration host still to be chosen; no Kubernetes commitment.
- Migrations owned by each schema owner, health/readiness and compatible versions for independent deployment.
- JSON structured logs with a correlation ID carried in HTTP headers and outbox events; `/health` and `/ready` per service; Prometheus metrics for errors, latency, outbox lag, triage job age, retries, failed rows, fallback, pending bookings and failed notifications.
- Grafana dashboard in the optional profile, initial version in S2; backup/restore and rollback documented/exercised before release.

## 8. Gaps and validation

Proposed in [ADR-001–006](adr/README.md) (team review pending): boundaries, stack, database, asynchronous mechanism, deployment contract, identity and calendar simulator. Still undecided: demonstration host, AI provider/model (DEC-05), accessible component library, final schemas and detailed rescheduling/cancellation handling; see the [unresolved details](planning/decisions-and-feedback.md#5-en-01-01-unresolved-details). The physical data model and executable contracts do not yet exist.

See [requirements](requirements.md), [security](09-security-privacy.md) and [walking skeleton](10-walking-skeleton.md).
