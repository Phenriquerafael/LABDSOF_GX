# Architecture Decision Records

**Status:** ADR-001 revised and ADR-002–006 proposed in EN-01-01 on 2026-10-15; team review pending, no ADR accepted

ADRs document context, alternatives, choice, consequences and evidence. “Proposed” does not justify claiming that a technology was selected or that a monolith exception was accepted.

| ADR | Topic | Status |
|---|---|---|
| [ADR-001](ADR-001-backend-boundaries.md) | Backend boundaries, data ownership and worker placement | Proposed (revised 2026-10-15) |
| [ADR-002](ADR-002-technology-stack.md) | Technology stack: client, runtime, framework, database and ORM | Proposed |
| [ADR-003](ADR-003-async-workflow-without-broker.md) | Asynchronous workflow without a broker: outbox, job claiming, relay, idempotency | Proposed |
| [ADR-004](ADR-004-deployment-contract.md) | Deployment contract: images, Compose, CI and host independence | Proposed |
| [ADR-005](ADR-005-authentication-authorisation.md) | Own authentication and membership-based authorisation | Proposed |
| [ADR-006](ADR-006-calendar-simulator.md) | External calendar simulator and reconciliation | Proposed |
| To be created after comparison | AI provider/contract and evaluation | Undecided in DEC-05 |

Use the [template](../templates/adr.md). States: Proposed → Accepted or Rejected; an accepted decision may be Superseded by a new ADR, preserving history. Record participants/date and links to evidence or feedback.

ADR-001–006 are proposals prepared by the Tech Lead in EN-01-01. They become Accepted only after a team review records participants and date, and an independent PR review. Product and process decisions belong in the [register](../planning/decisions-and-feedback.md).

## Evidence register (EN-01-01)

E01–E33 were accessed on 2026-10-15. Versions and terms change; re-check them before relying on a number after that date. Type: **O** = official documentation, release data or registry metadata; **C** = community/repository signal; **A** = assignment or project document. None of these sources is a benchmark run by the team.

| ID | Source | URL | Recorded fact |
|---|---|---|---|
| E01 | React releases | https://github.com/facebook/react/releases | O — 19.3.1 published 2026-10-14 |
| E02 | Vite on npm | https://registry.npmjs.org/vite | O — 8.3.4 published 2026-10-08; engines `^20.19.0 \|\| >=22.12.0` |
| E03 | Tailwind CSS on npm | https://registry.npmjs.org/tailwindcss | O — 4.3.3 published 2026-07-16; `@tailwindcss/vite` plugin at the same version |
| E04 | vite-plugin-pwa on npm | https://registry.npmjs.org/vite-plugin-pwa | O — 2.0.0 published 2026-10-03; peer range includes Vite 8 |
| E05 | Elysia on npm and GitHub | https://registry.npmjs.org/elysia · https://github.com/elysiajs/elysia | O/C — 1.4.30 published 2026-08-26; MIT; peer dependency on `@types/bun`; about 19.3k stars; top contributor 1,918 commits, next 30 |
| E06 | Elysia plugins on npm | https://registry.npmjs.org/@elysiajs/jwt · https://registry.npmjs.org/@elysiajs/openapi · https://registry.npmjs.org/@elysiajs/eden | O — jwt 1.4.2, openapi 1.4.16, eden 1.4.9 |
| E07 | Elysia Node adapter | https://elysiajs.com/integrations/node · https://github.com/elysiajs/node/issues | O/C — Node runs through an adapter (`@elysia/node` 1.4.6 on npm); 21 open issues including #58 “starts serving before async plugins resolve”, #59 headers dropped, #61 crash when bundled |
| E08 | Bun releases and image | https://github.com/oven-sh/bun/releases · https://hub.docker.com/r/oven/bun | O — 1.4.3 published 2026-10-10; `oven/bun:1.4` image for amd64 and arm64 |
| E09 | Bun SQL | https://bun.com/docs/runtime/sql | O — “compatible with MySQL 5.7+ and MySQL 8.0+” (9.x not stated) |
| E10 | Bun password hashing | https://bun.com/docs/runtime/hashing | O — `Bun.password` supports argon2id, argon2i, argon2d and bcrypt |
| E11 | Bun test runner; Vite with Bun | https://bun.com/docs/test · https://bun.com/guides/ecosystem/vite | O — Jest-compatible runner with mocks, coverage and hooks; Bun respects Vite's Node shebang unless `--bun` is passed |
| E12 | Node.js release schedule | https://github.com/nodejs/Release/blob/main/schedule.json | O — 22 maintenance from 2025-10-21, end 2027-04-30; 24 maintenance from 2026-10-20, end 2028-04-30; 26 LTS from 2026-10-28 |
| E13 | MySQL release model; Docker image | https://dev.mysql.com/doc/refman/8.4/en/mysql-releases.html · https://hub.docker.com/_/mysql | O — LTS and Innovation tracks; `mysql:lts` resolves to 9.7.2 (2026-10-12), amd64 and arm64 |
| E14 | MySQL locking reads | https://dev.mysql.com/doc/refman/8.4/en/innodb-locking-reads.html | O — `SKIP LOCKED` “may be used to avoid lock contention when multiple sessions access the same queue-like table” |
| E15 | Prisma packages on npm | https://registry.npmjs.org/@prisma/client · https://registry.npmjs.org/prisma | O — `@prisma/client` latest 7.10.0; the `prisma` CLI `latest` tag points to 8.0.0-rc.23 with 7.10.0 tagged `prev`; engines `^20.19 \|\| ^22.12 \|\| >=24.0` |
| E16 | Prisma MySQL and multi-schema docs | https://www.prisma.io/docs/orm/overview/databases/mysql · https://www.prisma.io/docs/orm/prisma-schema/data-model/multi-schema | O — MySQL 5.6+ supported; multi-schema “only supported for PostgreSQL, CockroachDB, and SQL Server” |
| E17 | Prisma Bun guide | https://www.prisma.io/docs/guides/bun | O — Bun supported; guide verified against Prisma Postgres, not MySQL |
| E18 | Prisma row-locking issue | https://github.com/prisma/orm/issues/5983 | C — “Add support for row-level locking (via `FOR UPDATE SKIP LOCKED`)” open |
| E19 | NestJS 12 | https://github.com/nestjs/nest/releases/tag/v12.0.0 · https://docs.nestjs.com/microservices/rabbitmq | O — v12.0.0 published 2026-08-27, “requires Node.js v20.19+ or v22.12+”; 12.1.3 on 2026-10-12; built-in RabbitMQ transport |
| E20 | RabbitMQ quorum queues and releases | https://www.rabbitmq.com/docs/quorum-queues · https://github.com/rabbitmq/rabbitmq-server/releases | O — classic mirroring removed in 4.0; delivery limit defaults to 20; 4.3.6 published 2026-09-14 |
| E21 | .NET and Spring Boot versions | https://dotnetcli.blob.core.windows.net/dotnet/release-metadata/releases-index.json · https://start.spring.io/metadata/client | O — .NET 10.0.13 LTS, end 2028-11-14; Spring Boot default 4.1.1 |
| E22 | Stack Overflow Developer Survey 2025 | https://survey.stackoverflow.co/2025/technology | O — web frameworks: Node.js 48.7%, React 44.7%, ASP.NET Core 19.7%, FastAPI 14.8%, Spring Boot 14.7%, NestJS 6.7%; TypeScript 43.6% |
| E23 | PostgreSQL versioning | https://www.postgresql.org/support/versioning/ | O — 18.6 current, supported to 2030-11-14; 19 RC1 on 2026-10-15 |
| E24 | Docker Compose in production | https://docs.docker.com/compose/how-tos/production/ | O — “The easiest way to deploy an application is to run it on a single server” |
| E25 | Render free tier | https://render.com/docs/free | O — spin-down after 15 minutes idle; free Postgres expires after 30 days; no persistent disks on free services |
| E26 | Fly.io pricing | https://docs.fly.io/about/pricing | O — new organisations: trial of 2 hours machine runtime or 7 days; plans with free allowances discontinued in October 2024 |
| E27 | Railway plans | https://docs.railway.com/reference/pricing/plans | O — trial: one-time USD 5 grant, 1 GB RAM per service; Hobby USD 5/month including USD 5 usage |
| E28 | Oracle Cloud Always Free | https://docs.oracle.com/en-us/iaas/Content/FreeTier/freetier_topic-Always_Free_Resources.htm | O — Ampere A1 (arm64) equivalent of 2 OCPUs and 12 GB memory |
| E29 | Keycloak sizing; Zitadel licence | https://www.keycloak.org/high-availability/multi-cluster/concepts-memory-and-cpu-sizing · https://github.com/zitadel/zitadel | O — Keycloak base memory 1250 MB per pod; Keycloak 26.8.1 (2026-10-13); Zitadel AGPL-3.0 |
| E30 | Gemini API terms and models | https://ai.google.dev/gemini-api/terms · https://ai.google.dev/gemini-api/docs/models | O — unpaid data may be used to improve products, but in the EEA/Switzerland/UK paid-service data terms apply to unpaid quota; users must be 18+; Gemini 2.0 Flash shut down; stable Flash models include `gemini-3.6-flash` and `gemini-3.8-flash` |
| E31 | Assignment §6 | [LABDSOF-26-27-Assignment.md](../../LABDSOF-26-27-Assignment.md) | A — §6.2 two deployable components; §6.3 async workflow; §6.4 one database technology preferred; §6.5 simulator accepted; §6.6 footnote allows mocked authentication |
| E32 | SQLite SELECT syntax | https://www.sqlite.org/lang_select.html | O — no `FOR UPDATE` or `SKIP LOCKED` clause in the documented syntax |
| E33 | Drizzle ORM MySQL drivers | https://orm.drizzle.team/docs/get-started-mysql | O — MySQL through `mysql2` (primary) and Bun SQL, among others |

Checked and not used as evidence: a claimed 2026 reduction date for Oracle Always Free and a Render free bandwidth figure (not found on the official pages), and broker memory figures that appear only in community blogs.
