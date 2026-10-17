# ADR-006 — External calendar simulator and reconciliation

**Status:** Proposed
**Date:** 2026-10-15
**Participants:** Ricardo (P2, Tech Lead) — proposer; Afonso (P3, integrations) to confirm; team review pending
**Related decision:** DEC-06
**Supersedes / superseded by:** —

## Context

§6.5 requires integration with an external system or a realistic simulator, handling unavailable, delayed, incomplete or invalid data (E31). The [design §6](../08-technical-design.md#6-external-integration-and-degraded-mode) lists the scenarios (success, timeout, unavailability, delay, incomplete/invalid response, scheduling conflict) and the rule that a timeout may happen after creation, so the client must query/reconcile before retrying.

## Alternatives

| Option | Benefits | Costs/risks | Evidence |
|---|---|---|---|
| **A. Custom calendar simulator service** | Every failure scenario can be triggered on demand in tests and demos; stable external references; no OAuth or quota | One more small component; must model only the external system | E31 §6.5 |
| B. Google Calendar API | Real external system | OAuth setup and secrets; quotas; failures cannot be forced on demand | — |
| C. Self-hosted CalDAV server | Real protocol | Weak scenario control; extra server | — |
| D. No integration | — | Fails §6.5 | E31 §6.5 |

## Proposed or accepted decision

**A**, proposed, not accepted:

- A separate Elysia-on-Bun container (`apps/calendar-sim`) exposing booking creation with an idempotency key and stable external reference, booking query by reference, and cancellation; documented with OpenAPI.
- A control endpoint (internal network only) selects the scenario per request or globally: success, timeout after creation, unavailable, delay, invalid/incomplete response, conflict.
- State is **in memory**, loaded from a fixed seed so it starts the same way every time. A simulator restart is a documented “external system lost data” scenario that Dispatch reconciliation must handle; the simulator does not share the MySQL instance.
- Dispatch uses a provider-neutral calendar adapter, so a real calendar can replace the simulator later behind the same port.
- Manual confirmation stays distinct from external synchronisation and suspends automatic re-creation until reconciliation (design §6).

## Consequences

- Integration failure tests (NFR-11) are reproducible in CI and demos.
- The simulator must not become a business component (design §1).
- A real calendar would add OAuth secrets handling and a new security review.

## Verification and review

Sprint B evidence: at least success, timeout-after-create with reconciliation, duplicate request (single booking), conflict, and simulator restart. EN-01-03 consolidates the integration view. Review if the lecturer requires a real provider.

## Record

2026-10-15 — proposed by Ricardo (Tech Lead) after a structured design interview for EN-01-01; drafting AI-assisted. Evidence: [evidence register](README.md#evidence-register-en-01-01) E31; [design §6](../08-technical-design.md#6-external-integration-and-degraded-mode); issue [#19](https://github.com/Phenriquerafael/LABDSOF_GX/issues/19); EN-01-01 pull request. Affected documents: technical design, DEC-06. Final decision, date and participants: pending team review.
