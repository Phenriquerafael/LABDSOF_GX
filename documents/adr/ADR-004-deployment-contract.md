# ADR-004 — Deployment contract, environments and pipeline

**Status:** Proposed
**Date:** 2026-10-15
**Participants:** Ricardo (P2, Tech Lead) — proposer; Martim (P5, DevOps) to confirm; team review pending
**Related decision:** DEC-04
**Supersedes / superseded by:** —

## Context

The topology has about six to eight containers: client (nginx), Issue, Dispatch, triage worker, calendar simulator, MySQL and an optional observability profile. The design excludes a Kubernetes commitment. The demonstration host is not yet known; the team wants the system to run on any Docker host rather than being tied to one provider. NFR-08 requires reproducible builds, containers, CI and configuration without secrets in Git; NFR-09 requires an exercised backup/restore.

## Alternatives

| Option | Benefits | Costs/risks | Evidence |
|---|---|---|---|
| **A. Host-agnostic contract: multi-arch images + Compose on any Docker host** | Same definition on laptops, a school VM, a VPS or an ARM free tier; failure injection is easy | Single host is a single point of failure; host OS, patching and backups are self-managed | E24, E28 |
| B. Render free tier | Managed and free | Free services spin down after 15 minutes idle; free Postgres expires after 30 days (and the project uses MySQL); no persistent disks on free services | E25 |
| C. Fly.io | Managed containers | No free tier for new organisations, trial only | E26 |
| D. Railway | Managed; usage billing | Trial is a one-time USD 5 grant; Hobby USD 5/month; demo-day cost risk | E27 |
| E. Kubernetes | Standard orchestration | Not committed in the design; operational burden | [design §7](../08-technical-design.md#7-proposed-deployment-and-operations) |

## Proposed or accepted decision

**A**, proposed, not accepted. The deployment contract is:

- Container images for each app published to GitHub Container Registry, built for **linux/amd64 and linux/arm64** (Oracle Always Free is Ampere A1/arm64, E28; MySQL and Bun images publish both, E08, E13).
- One `compose.yaml` (profiles: core, `observability`) and a `.env.example`; secrets only in the host environment or CI secrets, never in Git.
- GitHub Actions: install → lint → unit tests → integration tests with a MySQL 9.7 service container → multi-arch image build/push. Deployment is `docker compose pull && docker compose up -d` on the chosen host (E24); required checks are configured only when the pipeline exists.
- Only nginx is exposed; it serves the SPA and reverse-proxies `/api/issue/*` and `/api/dispatch/*` ([ADR-005](ADR-005-authentication-authorisation.md)). MySQL and internal endpoints stay on the internal Compose network.
- A short runbook: start/stop, logs, migrations, backup/restore exercise, rollback to the previous image tag.
- The demonstration host (school VM, Oracle Always Free, VPS) is an open item owned by Martim; it does not change this contract.

## Consequences

- No provider lock-in; the grader or a reviewer can run the system from a clone.
- Multi-arch builds lengthen CI; accepted to keep the host choice open.
- One host remains a single point of failure, stated as a limitation of the academic release.
- Observability containers are optional to respect host memory; memory needs are measured on the real host, not assumed.

## Verification and review

Before release: fresh clone → `docker compose up` → guided journey works on amd64 and arm64; backup/restore exercise recorded with counts and integrity checks (NFR-09). Review when the host is chosen.

## Record

2026-10-15 — proposed by Ricardo (Tech Lead) after a structured design interview for EN-01-01; drafting and source checks AI-assisted. Evidence: [evidence register](README.md#evidence-register-en-01-01) E08, E13, E24–E28; issue [#19](https://github.com/Phenriquerafael/LABDSOF_GX/issues/19); EN-01-01 pull request. Affected documents: [technical design §7](../08-technical-design.md#7-proposed-deployment-and-operations), DEC-04. Final decision, date and participants: pending team review.
