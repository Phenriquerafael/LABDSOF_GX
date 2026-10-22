# Building Maintenance Coordinator · LABDSOF 26/27

**BMC** is a proposed platform to help condominium administrators and residents coordinate faults: from the first report through intervention and closure, with traceable information and AI-assisted triage.

**Current stage: Sprint 1 — discovery, validation and design.** The repository contains planning documentation and templates. **It does not yet contain an application, software tests, CI/CD or executable deployment.**

**Confirmed Sprint 1 scope:** the evaluator clarified that the walking skeleton is optional, and the team decided not to develop it this sprint. This is recorded in [DEC-11](documents/planning/decisions-and-feedback.md#confirmed-sprint-1-scope-decision--dec-11); later implementation requirements remain applicable.

## Start here

1. Read the [assignment](LABDSOF-26-27-Assignment.md).
2. Learn about the [strategy](documents/building-maintenance-coordinator-strategy.md) and [vision/scope](documents/04-product-vision.md).
3. Consult the [Sprint 1 plan](documents/planning/sprint-1-plan.md), [backlog](documents/05-product-backlog.md) and [pending decisions](documents/planning/decisions-and-feedback.md).
4. To contribute, use the [team working agreement](documents/governance/team-working-agreement.md), [DoR](documents/governance/definition-of-ready.md), [DoD](documents/governance/definition-of-done.md) and [Git workflow](documents/governance/git-workflow.md).

## Proposed product

**Report → review triage → select contractor → approve request → obtain acceptance → confirm booking → execute → close.**

- Community: residential condominiums in Portugal.
- Proposed primary user: professional administrator; residents and contractors participate in the workflow.
- Outcome to investigate: reduce the administrator's active coordination time.
- AI: correctable suggestions, with a non-AI baseline and fallback; actions approved by the authorised user.
- Proposed MVP: text, small catalogue, one notification channel, calendar/simulator, states and history.
- Post-MVP: photos, cost analytics and narrative summaries.
- **IoT is proposed as excluded from the MVP** (DEC-01, BA/PO proposal of 2026-10-19, team agreement pending).

<!-- ## How to interpret statuses

**Draft/proposal** = written but not validated. **Plan/template** = a way to perform/record work, not proof of execution. **Completed/verified** requires evidence (PR, result, configuration or session). Checked boxes below refer only to what they explicitly describe.

**Latest team update recorded:** 2026-10-10 — member assignments, P1/P3 cross-cutting role swap and optional Sprint 1 skeleton decision. Other proposals and working practices still require review/ratification. Documentation prepared with AI assistance; claims, choices and results require human review and evidence. -->

## Product documents and Sprint 1 deliverables

File numbering matches the assignment deliverables; document 08 links to the ADR folder and index.

| Document | §7.1.2 | Documentation status | What remains for completion/validation |
|---|---|---|---|
| [Strategy](documents/building-maintenance-coordinator-strategy.md) | Overview | Harmonised proposal | Scope and segment decisions |
| [01 — Problem and opportunity](documents/01-problem-and-opportunity-report.md) | 1 | Draft | Problem evidence |
| [02 — Market and competitors](documents/02-market-and-competitor-analysis.md) | 2 | Preliminary survey | Verified sources and comparison |
| [03 — Research](documents/03-user-research.md) | 3 | Plan and guides | Collection, findings, limitations and decisions |
| [04 — Vision](documents/04-product-vision.md) | 4 | Proposal | Validation of personas, scope and metrics |
| [05 — Product Backlog](documents/05-product-backlog.md) | 5 | Proposed epics, criteria and MoSCoW | Prioritise with stakeholders, estimate and assign |
| [Functional requirements and NFRs](documents/requirements.md) | Supports 4/5/7 | Initial list | Validate needs and targets |
| [06 — Responsible AI Assessment](documents/06-responsible-ai-assessment.md) | 6 | Workflow and evaluation proposal | Validated need, provider, dataset and criteria |
| [07 — Technical Design](documents/07-technical-design.md) | 7 | Domain model, state rules and contract outlines (EN-01-02 proposal) with [contract examples](documents/07a-contract-examples.md) | Stack decisions (EN-01-01), rule validation (DEC-10) and executable contracts |
| [08 — Architecture Decision Records](documents/08-architecture-decision-records.md) / [ADR-001](documents/adr/ADR-001-backend-boundaries.md) | 8 | One proposed ADR | Choices and consequences accepted with evidence |
| [09 — Security and privacy](documents/09-security-privacy.md) | 9 | Initial assessment | Policies/review; controls in implementation |
| [10 — Walking Skeleton](documents/10-walking-skeleton.md) | 10, optional per evaluator | Not selected for Sprint 1 (DEC-11) | Plan retained for later implementation; not a Sprint 1 blocker |
| [Original notes](NOTAS.md) | Exploration | Ideas preserved and contextualised | Validate suggestions and provider information |

## Organisation, planning and templates

| Document | Status |
|---|---|
| [Team working agreement and roles](documents/governance/team-working-agreement.md) | Named assignments agreed; remaining working practices await ratification |
| [Definition of Ready](documents/governance/definition-of-ready.md) | Proposed checklist |
| [Definition of Done](documents/governance/definition-of-done.md) | Proposal by delivery type |
| [Git workflow](documents/governance/git-workflow.md) | Proposal; remote protections not verified |
| [Issues, labels and milestones](documents/governance/issue-management.md) | Local conventions; remote configuration pending |
| [Sprint 1 plan](documents/planning/sprint-1-plan.md) | Phases, dependencies and exit criteria |
| [Epic and Sprint 1 task issue drafts](documents/backlog/README.md) | 5 epics, 4 parent tasks and 15 delivery tasks; proposed allocations; remote publication unverified and no new sprint commitments |
| [Sprint B backlog](documents/planning/sprint-b-backlog.md) | Candidates; no Ready commitments |
| [Decisions and feedback](documents/planning/decisions-and-feedback.md) | Evaluator clarification and team decisions recorded; other questions remain open |
| [Story](.github/ISSUE_TEMPLATE/user-story.md), [epic](.github/ISSUE_TEMPLATE/epic.md), [task/spike](.github/ISSUE_TEMPLATE/task.md), [bug](.github/ISSUE_TEMPLATE/bug.md) | Local GitHub templates; labels/milestones to select after setup |
| [Pull request](.github/pull_request_template.md) | Local template |
| [ADR](documents/templates/adr.md), [research](documents/templates/research-record.md), [review](documents/templates/investor-review.md) | Templates to copy and complete with real data |

## Sprint 1 phase progress

### Phase 1 — organisation · partial

- [x] Local Git repository available.
- [x] Documented proposals for methodology, DoR/DoD, Git and tracking.
- [x] Local collaboration templates prepared.
- [ ] Verify remote, access, main branch and protections.
- [ ] Choose/configure board, labels, milestones and issues.
- [x] Record agreed member assignments and responsibilities (Pedro/PM; Afonso/BA-PO).
- [ ] Ratify methodology/DoR/DoD.
- [ ] Confirm dates, capacity and the meaning of Sprint B.

### Phase 2 — discovery · documentation preparation

- [x] Problem, hypotheses and research guides written.
- [x] Proposed initial list of features and NFRs.
- [ ] Complete domain research and competitor sources.
- [ ] Perform elicitation and record evidence/limitations.
- [ ] Update requirements and decisions based on findings.

### Phase 3 — scope and backlog · proposal

- [x] Vision, provisional personas, journeys, epics and conceptual model.
- [x] Backlog with criteria and proposed MoSCoW priorities.
- [x] Candidates and DoR gate for Sprint B.
- [ ] Discuss priorities with the customer/lecturers and record changes.
- [ ] Estimate, assign owners and select only Ready items.
- [ ] Consolidate architecture, AI, security and ADRs.
- [x] Record the decision not to develop the optional walking skeleton in Sprint 1 (DEC-11).
- [ ] Record investor review and the product/technical decision revision required by §5.7; evaluator scope clarification is recorded separately.

## Priority decisions

DEC-01: scope and IoT; DEC-03: team/schedule/Sprint B; DEC-04: stack and architecture; DEC-07: access and data; DEC-09: process/board; DEC-10: intervention rules. Other questions and participants are in the [register](documents/planning/decisions-and-feedback.md).

**Next team session:** ratify organisation, assign owners and prepare research/decisions for planning. Phases can run in parallel without declaring research complete before it has taken place.

## Run the project

There is no application to run yet. The [walking skeleton plan](documents/10-walking-skeleton.md) is retained for later implementation planning, with fields for actual commands, environment and evidence. The team has excluded its development from Sprint 1 following the evaluator's clarification.
