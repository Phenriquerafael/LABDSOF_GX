# Pending decisions and feedback

**Status:** open decisions and confirmed team updates · **Updated:** 2026-10-15

A provisional baseline enables consistent documents; it does not mean approval. Named role holders are recorded in the [team agreement](../governance/team-working-agreement.md); item owners and deadlines still require assignment. When deciding, record evidence, author, date and affected documents. The recording dates below do not imply that the meetings occurred on those dates.

## 1. Open decisions

| ID | Question | Provisional baseline / alternatives | Participants (roles mapped in team agreement) | Timing | Status |
|---|---|---|---|---|---|
| DEC-01 | Coordination or IoT? | Coordination; one possible IoT scenario; broad monitoring requires a new scope | Team, BA/PO, lecturers | Before finalising the MVP | Undecided |
| DEC-02 | Segment and commercial model | Property management companies; volunteers secondary; SaaS is a hypothesis | BA/PO, administrators | After initial research | To be validated |
| DEC-03 | Team, schedule, capacity and Sprint B | Five named members and responsibilities agreed; P1 = PM, P3 = BA/PO. Three official sprints; phases 1–3 within Sprint 1; do not assume B = Sprint 2 | Pedro (PM), team, lecturers | Before committing to Sprint B | Assignments confirmed; schedule, capacity and Sprint B unresolved |
| DEC-04 | Stack, boundaries and infrastructure | Proposed in EN-01-01 on 2026-10-15 ([ADR-001–005](../adr/README.md)): Issue + Dispatch services and triage worker; React + Vite PWA shell with Tailwind; Elysia on Bun; MySQL 9.7 with a database per service; Prisma 7.10; outbox and job tables without a broker; multi-arch images and Compose on any Docker host; own login with JWT cookies and membership RBAC. No modular-monolith exception requested | Tech Lead, DevOps, team | Sprint 1 design; before implementation | Proposed; team review pending; open details in §5 |
| DEC-05 | AI workflow, provider and evaluation | Text classification, baseline/fallback, one provider; optional embeddings | AI, QA, BA/PO | Before the AI story meets DoR | Undecided |
| DEC-06 | Integration and notifications | Proposed in EN-01-01 on 2026-10-15: custom calendar simulator with fault scenarios, in-memory state and reconciliation ([ADR-006](../adr/ADR-006-calendar-simulator.md)); real calendar optional later; in-app notifications as a module of the Issue Service ([ADR-001](../adr/ADR-001-backend-boundaries.md), [ADR-003](../adr/ADR-003-async-workflow-without-broker.md)) | Tech Lead, BA/PO | Before integration | Proposed; team review pending; channel detail open in §5 |
| DEC-07 | Visibility and data lifecycle | Private reports, published shared summary, extract for contractor, synthetic data | QA/Security, BA/PO | Before implementing permissions | To be validated |
| DEC-08 | Baselines and targets | Administrative effort as primary metric; provisional technical targets | QA, BA/PO, AI | Before formal evaluation | To be calibrated |
| DEC-09 | Methodology, board and Git | Adapted Scrum, GitHub Projects or Jira, short-lived branches and reviewed PRs | Team | Phase 1 | To be ratified/configured |
| DEC-10 | Approval and confirmation | Administrator approves request; contractor accepts; calendar/manual confirmation; administrator closes | BA/PO, administrators and contractors | Before US-07–US-09 | To be validated |

### Confirmed Sprint 1 scope decision — DEC-11

- **Recorded:** 2026-10-10, from the team's update in the project conversation.
- **Clarification:** the evaluator confirmed that the walking skeleton is optional for Sprint 1.
- **Team decision:** do not develop the walking skeleton this sprint. Its absence is not a Sprint 1 completion blocker; it is not marked as implemented.
- **Change:** the earlier plan treated §7.1.2 item 10 as required in Sprint 1. The revised plan excludes that implementation and retains EN-02 as a candidate foundation for later implementation planning.
- **Scope:** other Sprint 1 discovery/design deliverables and later technical delivery requirements remain applicable. The original assignment is retained unchanged.
- **Traceability:** README, strategy, roadmap, backlog, Sprint 1/Sprint B plans, walking skeleton plan, team agreement and milestone conventions updated. Evaluator name, clarification date and meeting link were not supplied.

## 2. Editorial harmonisation

These are neither investor feedback nor user-validated choices.

| Conflict | Treatment on 2026-10-07 |
|---|---|
| 05 treated as backlog | Roadmap retained; 06-product-backlog created |
| MVP ended at notification | Completion, closure and history made explicit |
| Approval confused with scheduling | Approval, acceptance and confirmation separated |
| Photos/costs/summaries in Sprint 3 and after the course | Post-MVP, without Sprint 3 commitment |
| Automatic vs human approval | Automatic approval outside baseline |
| 100% of critical cases as a guarantee | Criterion restricted to the test set |
| Not found = nonexistent | Comparison marked preliminary/unconfirmed |
| IoT in notes vs vision scope | Preserved for DEC-01 |
| Git protections and board as facts | Proposed configurations without remote evidence |

## 3. Stakeholder/investor feedback

The evaluator clarification below was reported by the team. No separate investor review session or revision of a product/technical decision has been recorded; the scope clarification alone is not treated as proof of meeting §5.7.

| ID | Date/participants | Feedback and evidence | Previous → revised decision | Reason | Documents/issues | Owner/status |
|---|---|---|---|---|---|---|
| FB-01 | Recorded 2026-10-10; team reports evaluator clarification; session date/name not supplied | Walking skeleton optional in Sprint 1; source: team update in project conversation | Required Sprint 1 skeleton → no skeleton development this sprint | Team chose not to implement the optional deliverable | DEC-11; EN-02; Sprint 1 plan | Team decision confirmed; Pedro coordinates planning updates |

Use the [template](../templates/investor-review.md). At least one decision must actually be revised in response to lecturers (§5.7). Minutes without a change do not meet that requirement.

## 4. Limitations and technical debt

| ID | Limitation | Impact | Next action | Owner |
|---|---|---|---|---|
| LIM-01 | No primary research recorded | Value and adoption need validation | DISC-01 | To be assigned |
| LIM-02 | Incomplete competitor sources | Differentiation not demonstrated | DISC-02 | To be assigned |
| LIM-03 | No application code, CI or deployment in this baseline | Later technical requirements not yet demonstrated; optional skeleton excluded from Sprint 1 by DEC-11 | EN-01 design; EN-02 considered in later implementation planning | To be assigned |

Add implementation debt when it exists; future features are not automatically technical debt.

## 5. EN-01-01 unresolved details

Recorded 2026-10-15 by Ricardo (Tech Lead) for [#19](https://github.com/Phenriquerafael/LABDSOF_GX/issues/19). Owners are proposals until the team review confirms them.

| ID | Open detail | Proposed owner | Impact if unresolved | Needed by |
|---|---|---|---|---|
| EN01-U1 | Team review of ADR-001–006, including written confirmation that the team is comfortable with TypeScript | Ricardo (facilitates), team | ADRs stay Proposed; EN-01-02/03/04 build on proposals | EN-01-01 closure |
| EN01-U2 | Spike (≤1 day): Prisma 7.10 on Bun against MySQL 9.7 (connector or `@prisma/adapter-mariadb`), raw `SKIP LOCKED` claim with concurrent workers, Elysia JWT/OpenAPI/Eden together, JSON logger on Bun | Ricardo, Afonso | Failure reopens ADR-002 (fallback NestJS 12 on Node 24) | Before Sprint B Ready |
| EN01-U3 | Demonstration host and budget (school VM, Oracle Always Free, VPS) | Martim | Runbook and release environment; the deployment contract itself is unaffected | Sprint B planning |
| EN01-U4 | Accessible component library on top of Tailwind | Pedro | NFR-07 keyboard/ARIA behaviour; first UI story cannot meet DoR | Before the first UI story is Ready |
| EN01-U5 | AI provider, model and tier (Gemini Flash family is the team's candidate; check EEA data terms and the 18+ requirement) | Sandro (DEC-05, AI-01) | US-03 cannot meet DoR | AI-01 |
| EN01-U6 | Authentication details: argon2id parameters, key storage and rotation, token lifetimes, CSRF assumptions | Martim with Ricardo (EN-01-04) | Security review incomplete; NFR-01 evidence | EN-01-04 |
| EN01-U7 | Retry limits, backoff, polling intervals and lease durations for outbox and triage jobs | Ricardo, Sandro (EN-01-02) | NFR-04 tests lack target values | EN-01-02 |
| EN01-U8 | Notification channel detail beyond the in-app baseline | Afonso | DEC-06 cannot close | EN-01-03 |
| EN01-U9 | Test tooling confirmation (`bun test`, Vitest, MySQL service container) and CI layout | Martim | CI design | Sprint B setup |
| EN01-U10 | Re-check pins: Node 26 LTS (from 2026-10-28) and Prisma 8 status | Martim | Version drift | Sprint B planning |
