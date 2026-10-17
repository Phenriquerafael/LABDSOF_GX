# ADR-003 — Asynchronous workflow without a broker

**Status:** Proposed
**Date:** 2026-10-15
**Participants:** Ricardo (P2, Tech Lead) — proposer; team review pending
**Related decision:** DEC-04; supports DEC-06 (notifications)
**Supersedes / superseded by:** —

## Context

§6.3 requires at least one meaningful asynchronous workflow with retries, duplicate handling, failures and eventual consistency (E31); NFR-04 requires that redelivery does not duplicate effects, that retries are limited with an observable final failure, and that a crash between persistence and publication is tested. The [design](../08-technical-design.md#5-asynchronous-workflow-and-consistency) already proposes a transactional outbox and idempotent consumers. The assignment does not require a message broker. The team judged a broker to be implementation and operational complexity that the academic MVP does not need.

## Alternatives

| Option | Benefits | Costs/risks | Evidence |
|---|---|---|---|
| **A. Database outbox and job tables, `SKIP LOCKED` claiming, HTTP relay between services** | No extra infrastructure or protocol; the outbox is needed anyway to avoid losing events after a commit; failed rows are easy to inspect and demonstrate | Retry, backoff and dead-letter logic written by the team; polling latency (about 1–2 s); delivery depends on the receiver's API being up | E14; E31 §6.3 |
| B. RabbitMQ 4.3 with outbox publisher and quorum queues | Mature retries and dead-lettering (delivery limit defaults to 20); management UI | Additional container, AMQP concepts, topology to configure; still needs the outbox | E19, E20 |
| C. NATS JetStream or Redis Streams | Lighter than RabbitMQ | Additional technology; persistence and retry configuration still to learn | — |
| D. Kafka | Replayable log at scale | Disproportionate for project scale | — |

## Proposed or accepted decision

**A**, proposed, not accepted:

1. **Outbox.** Each service writes domain changes and an `outbox` row in the same database transaction. Envelope unchanged from the design: `eventId, eventType, schemaVersion, aggregateId, aggregateVersion, buildingId, occurredAt, correlationId` + minimal payload.
2. **Triage jobs.** Report submission creates a triage job in the Issue database. The triage worker calls Issue's internal API (`POST /internal/triage-jobs/claim`, `POST /internal/triage-jobs/{id}/complete`, service JWT per [ADR-005](ADR-005-authentication-authorisation.md)); Issue claims rows with `SELECT … FOR UPDATE SKIP LOCKED` and a lease time, so a crashed worker's job returns to the queue. Reporting never waits on AI (NFR-03).
3. **Cross-service delivery.** A relay loop inside each service reads pending outbox rows and POSTs them to the other service's internal event endpoint (for example Dispatch → Issue booking state; Issue → Dispatch cancellation). The request that created the event has already completed, so delivery is asynchronous.
4. **Retries and failure.** Rows carry `attempts`, `nextAttemptAt` (increasing delay) and a status; after a configured limit the row becomes `failed`. Failed rows are the dead-letter equivalent: counted in metrics, listed for administrators/operators, replayable by a controlled action.
5. **Idempotency.** Receivers record processed `eventId`s in a `processed_events` table in the same transaction as the effect; repeated deliveries are acknowledged without repeating the effect. Out-of-order events are rejected or ignored by `aggregateVersion`. “Exactly once” is not promised.
6. **Notifications.** The in-app notification module in Issue consumes state events through the same mechanism (DEC-06 baseline).
7. **Swap path.** If load or a later requirement justifies it, the relay can publish to a broker (RabbitMQ is the recorded candidate) without changing the envelope, outbox or idempotent receivers.

Prisma has no row-locking API (E18), so the claim is one reviewed raw-SQL function per service with an integration test ([ADR-002](ADR-002-technology-stack.md)).

## Consequences

- Fewer moving parts: no broker container, credentials or topology.
- The team owns retry/backoff/dead-letter code; it must be small, shared through `packages/contracts` conventions and tested.
- Polling adds load and latency; intervals are configuration and must stay modest on the shared MySQL instance.
- If the receiving service is down, events accumulate as pending rows and are delivered on recovery; the sender remains usable (eventual consistency, visible timestamps).
- `SKIP LOCKED` statements are unsafe for statement-based replication (E14); irrelevant for the single-instance MVP, recorded for later.
- The architecture explanation must make clear that HTTP delivery from the outbox is asynchronous with respect to the user request.

## Verification and review

Required evidence when the first asynchronous slice is built: crash after commit and before delivery (event still delivered); duplicate delivery (single effect); two workers claiming concurrently (no double claim); exhausted retries (row `failed`, visible in metrics, replay works); receiver down then up (eventual delivery). Review at Sprint B planning or if polling load or latency becomes a problem.

## Record

2026-10-15 — proposed by Ricardo (Tech Lead) after a structured design interview for EN-01-01; drafting and source checks AI-assisted. Evidence: [evidence register](README.md#evidence-register-en-01-01) E14, E18–E20, E31; issue [#19](https://github.com/Phenriquerafael/LABDSOF_GX/issues/19); EN-01-01 pull request. Affected documents: [technical design §5](../08-technical-design.md#5-asynchronous-workflow-and-consistency), DEC-04/06. Final decision, date and participants: pending team review.
