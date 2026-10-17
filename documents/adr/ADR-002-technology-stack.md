# ADR-002 — Technology stack

**Status:** Proposed
**Date:** 2026-10-15
**Participants:** Ricardo (P2, Tech Lead) — proposer; team review pending
**Related decision:** DEC-04
**Supersedes / superseded by:** —

## Context

No application code exists yet. Five students deliver over three sprints; implementation starts after Sprint 1 (DEC-11). The stack must support explicit service boundaries ([ADR-001](ADR-001-backend-boundaries.md)), one asynchronous workflow ([ADR-003](ADR-003-async-workflow-without-broker.md)), one database technology (E31 §6.4), container deployment without Kubernetes ([ADR-004](ADR-004-deployment-contract.md)) and an accessible client (NFR-07). The Tech Lead reports that the team is comfortable with TypeScript; no written skills survey is recorded, so the review should confirm this premise.

## Alternatives

### Language and backend framework

| Option | Benefits | Costs/risks (operational and learning) | Evidence |
|---|---|---|---|
| **A. TypeScript: Elysia on Bun** | One language end to end; Eden typed client shares API types with React; built-in OpenAPI and JWT plugins; Bun includes MySQL client, argon2id hashing and a test runner | Smaller ecosystem; single-maintainer bus factor; no built-in dependency injection or module system; Bun is a second JS runtime next to Node for the client build | E05, E06, E08–E11, E22 |
| B. TypeScript: NestJS 12 on Node 24 | Modules and DI make boundaries explicit; large ecosystem; built-in RabbitMQ transport | More framework concepts to learn; decorator-heavy; no end-to-end typed client without extra tooling | E12, E19, E22 |
| C. TypeScript: Elysia on Node (adapter) | Keeps one runtime | Adapter has open startup, header and bundling defects | E07 |
| D. C#: ASP.NET Core on .NET 10 LTS | Mature tooling; LTS to 2028-11-14 | Second language beside a TypeScript client; no recorded team depth | E21, E22 |
| E. Java: Spring Boot 4.1 | Deepest enterprise ecosystem | Second language; JVM memory and startup; steepest learning curve for the team | E21, E22 |
| F. Python: FastAPI | Close to AI tooling | Second language; fewer conventions for two domain services | E22 |

### Database and data access

| Option | Benefits | Costs/risks | Evidence |
|---|---|---|---|
| **MySQL 9.7 LTS, database per service** | Team preference; LTS track; `SKIP LOCKED` for queue-like tables; official arm64 image | No schema namespaces inside one database (a database per service is used instead); Bun SQL documents 5.7/8.0 only | E09, E13, E14 |
| PostgreSQL 18 | Supported to 2030; richer types | Not the team's choice; no requirement that MySQL fails | E23 |
| MySQL for production plus SQLite for development/tests | Fast local start | Second database technology (E31 §6.4); SQLite has no `FOR UPDATE SKIP LOCKED` and different typing, so tests would not prove MySQL behaviour | E14, E32 |
| **Prisma ORM 7.10** | Team preference; typed client; `prisma migrate` per service; MySQL supported | No row-locking API (raw SQL needed for job claiming); multi-schema unsupported on MySQL (not needed); Bun + MySQL not shown in official guides | E15–E18 |
| Drizzle ORM | SQL-like API; MySQL via `mysql2` or Bun SQL | Not chosen by the team | E33 |

### Client

| Option | Benefits | Costs/risks | Evidence |
|---|---|---|---|
| **React 19 + Vite 8 SPA, PWA (installable shell), Tailwind 4** | Static build served by nginx; large ecosystem; PWA plugin supports Vite 8 | Tailwind gives no keyboard/ARIA behaviour; an accessible primitives library is still needed | E01–E04 |
| Next.js | Popular full-stack framework | Server runtime and SSR not needed for an authenticated tool | E22 |
| Native/cross-platform mobile app | App-store presence | Second UI codebase; administrator workflows are desktop-first | E31 §6.1 |

## Proposed or accepted decision

Proposed, not accepted:

| Layer | Choice | Pin |
|---|---|---|
| Client | React + Vite SPA, responsive, PWA limited to install and cached app shell (reporting needs a connection), Tailwind CSS; served by nginx | React 19.3, Vite 8.3, Tailwind 4.3, vite-plugin-pwa 2.0; client build on Node 24 LTS |
| Backend services and worker | Elysia on Bun for Issue, Dispatch, triage worker and calendar simulator | Bun 1.4.x, Elysia 1.4.x (exact versions in lockfile) |
| API contracts | OpenAPI from `@elysiajs/openapi`; Eden typed client for the React app | Plugin versions in lockfile |
| Database | MySQL 9.7 LTS; databases `issue` and `dispatch`, one user each | `mysql:9.7` image |
| Data access | Prisma ORM, one schema/client/migration history per service; raw SQL only for job claiming | `prisma` and `@prisma/client` exactly 7.10.0 (not the 8.0 RC) |
| Monorepo | This repository, Bun workspaces: `apps/client`, `apps/issue`, `apps/dispatch`, `apps/triage-worker`, `apps/calendar-sim`, `packages/contracts` | One `bun.lock` |
| Tests | `bun test` for backend, Vitest for client, integration tests against a MySQL 9.7 container; no SQLite | Confirmed by Martim at Sprint B setup |

Elysia risks and mitigations: pin exact versions; keep plugins to jwt, openapi and eden; enforce boundaries with one app per service and lint rules that forbid imports between apps (shared code only through `packages/contracts`). NestJS 12 on Node 24 (option B) is the recorded fallback if the spike or Sprint B shows Elysia/Bun blocking progress.

## Consequences

- One language across client, services and contracts; type changes surface at compile time in both client and services.
- Two runtimes to patch: Bun for services, Node 24 for the client build stage only.
- Prisma cannot express `SELECT … FOR UPDATE SKIP LOCKED`; one reviewed raw-SQL function per service claims jobs, covered by an integration test ([ADR-003](ADR-003-async-workflow-without-broker.md)).
- Separate MySQL databases keep ownership enforceable by grants; cross-service data comes only through APIs or events.
- An accessible component library on top of Tailwind must be chosen before the first UI story is Ready.

## Verification and review

A spike of at most one day (owners: Ricardo and Afonso; before Sprint B Ready) must show: Prisma 7.10 on Bun against MySQL 9.7 (built-in connector or `@prisma/adapter-mariadb`); the raw `SKIP LOCKED` claim with two concurrent workers never claiming the same row; Elysia JWT, OpenAPI and Eden working together; a JSON logger working on Bun. Failure of any item reopens this ADR. Review again at Sprint B planning (Node 26 LTS from 2026-10-28; Prisma 8 status).

## Record

2026-10-15 — proposed by Ricardo (Tech Lead) after a structured design interview for EN-01-01; drafting and source checks AI-assisted. Evidence: [evidence register](README.md#evidence-register-en-01-01) E01–E23, E31–E33; issue [#19](https://github.com/Phenriquerafael/LABDSOF_GX/issues/19); EN-01-01 pull request. Affected documents: [technical design](../08-technical-design.md), DEC-04. Final decision, date and participants: pending team review.
