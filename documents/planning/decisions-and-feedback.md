# Pending decisions and feedback

**Status:** open decisions and confirmed team updates · **Updated:** 2026-10-19

A provisional baseline enables consistent documents; it does not mean approval. Named role holders are recorded in the [team agreement](../governance/team-working-agreement.md); item owners and deadlines still require assignment. When deciding, record evidence, author, date and affected documents. The recording dates below do not imply that the meetings occurred on those dates.

## 1. Open decisions

| ID | Question | Provisional baseline / alternatives | Participants (roles mapped in team agreement) | Timing | Status |
|---|---|---|---|---|---|
| DEC-01 | Coordination or IoT? | Coordination only; IoT, monitoring and prediction excluded from the MVP; reopen only through a scope review ([vision §4.2](../04-product-vision.md#42-iot-decision-dec-01)) | Team, BA/PO, lecturers | Before finalising the MVP | Proposed by BA/PO on 2026-10-19 (DISC-03-03); team agreement pending |
| DEC-02 | Segment and commercial model | Property management companies; volunteers secondary; SaaS is a hypothesis | BA/PO, administrators | After initial research | To be validated |
| DEC-03 | Team, schedule, capacity and Sprint B | Five named members and responsibilities agreed; P1 = PM, P3 = BA/PO. Three official sprints; phases 1–3 within Sprint 1; do not assume B = Sprint 2 | Pedro (PM), team, lecturers | Before committing to Sprint B | Assignments confirmed; schedule, capacity and Sprint B unresolved |
| DEC-04 | Stack, boundaries and infrastructure | Independent Issue + Dispatch; worker/queue; modular alternative subject to acceptance | Tech Lead, DevOps, team | Sprint 1 design; before implementation | Proposed ADR-001 |
| DEC-05 | AI workflow, provider and evaluation | Text classification, baseline/fallback, one provider; optional embeddings | AI, QA, BA/PO | Before the AI story meets DoR | Undecided |
| DEC-06 | Integration and notifications | Realistic simulated calendar; real optional; one proposed in-app channel | Tech Lead, BA/PO | Before integration | Undecided |
| DEC-07 | Visibility and data lifecycle | Private reports, published shared summary, extract for contractor, synthetic data | QA/Security, BA/PO | Before implementing permissions | To be validated |
| DEC-08 | Baselines and targets | Primary: active administrative time, ≥ 30% reduction in the median of participant medians versus the current process, with completion and critical-case guards ([vision §5.1](../04-product-vision.md#51-primary-success-criterion-dec-08)); provisional technical targets | QA, BA/PO, AI | Before formal evaluation | Product threshold proposed by BA/PO on 2026-10-19, team agreement pending; technical targets to be calibrated |
| DEC-09 | Methodology, board and Git | Adapted Scrum, GitHub Projects or Jira, short-lived branches and reviewed PRs | Team | Phase 1 | To be ratified/configured |
| DEC-10 | Approval and confirmation | Administrator approves request; contractor accepts; calendar/manual booking confirmation; contractor reports completion; administrator closes ([vision §4.3](../04-product-vision.md#43-distinct-steps-dec-10)) | BA/PO, administrators and contractors | Before US-07–US-09 | Step definitions proposed on 2026-10-19; transition, cancellation and concurrency rules proposed in EN-01-02 on 2026-10-19; validation with administrators and contractors pending |

### Confirmed Sprint 1 scope decision — DEC-11

- **Recorded:** 2026-10-10, from the team's update in the project conversation.
- **Clarification:** the evaluator confirmed that the walking skeleton is optional for Sprint 1.
- **Team decision:** do not develop the walking skeleton this sprint. Its absence is not a Sprint 1 completion blocker; it is not marked as implemented.
- **Change:** the earlier plan treated §7.1.2 item 10 as required in Sprint 1. The revised plan excludes that implementation and retains EN-02 as a candidate foundation for later implementation planning.
- **Scope:** other Sprint 1 discovery/design deliverables and later technical delivery requirements remain applicable. The original assignment is retained unchanged.
- **Traceability:** README, strategy, roadmap, backlog, Sprint 1/Sprint B plans, walking skeleton plan, team agreement and milestone conventions updated. Evaluator name, clarification date and meeting link were not supplied.

### Product scope proposals — DISC-03-03

- **Recorded:** 2026-10-19 by Afonso (BA/PO) for [#14](https://github.com/Phenriquerafael/LABDSOF_GX/issues/14). Team agreement is sought through the pull-request review (proposed reviewer: Martim); until then these are proposals, not stakeholder or lecturer approval, and not evidence for §5.7.
- **Evidence used:** secondary findings PS-01–PS-08 ([03 §7.2](../03-user-research.md#72-findings)) and competitor sources S01–S28 ([02 §5](../02-market-and-competitor-analysis.md#5-source-and-findings-register)). No primary research exists; DISC-01-03 is open, so personas and journeys keep their remaining hypotheses explicit.
- **DEC-01:** exclude IoT from the MVP. Reasons: not required by the assignment; no finding identifies a decision that sensor data would improve; a suite marketed in Portugal already bundles hardware (S04); capacity. Alternative considered: one simulated sensor scenario as Could — rejected because it would not test BMC's proposition.
- **DEC-08:** primary threshold of a ≥ 30% reduction in active administrative time on equivalent scripted tasks, computed as the median of participant medians against a pre-recorded answer key, with incomplete tasks recorded at the time cap and completion and critical-case guards. Alternatives: 20% (hard to separate from noise in a three-to-five-person sample) and 50% (likely to fail on factors outside the product). The rationale is team judgement, not a sourced benchmark; baseline limitations are disclosed in the vision.
- **DEC-10:** triage decision, approval, acceptance, booking confirmation, completion report, closure and impact confirmation defined as distinct steps, consistent with [07 §3](../07-technical-design.md#3-states-and-business-rules).
- **DEC-07 input:** the impact-confirmation review adds neutral count wording, no confirmation list for administrators and an open option on exact counts in very small buildings; the policy itself stays with DEC-07.
- **Affected documents:** [vision](../04-product-vision.md), [strategy](../building-maintenance-coordinator-strategy.md), [requirements](../requirements.md), [backlog §5](../05-product-backlog.md#5-wont-in-this-baseline--alternatives), [README](../../README.md). The IoT row of the former roadmap is carried by backlog §5 since the roadmap was removed in #16. US-04 criteria are unchanged; the counter wording is a refinement for the US-04 owner to consider.

### Domain and contract proposals — EN-01-02

- **Recorded:** 2026-10-19 by Afonso (BA/PO) for [#20](https://github.com/Phenriquerafael/LABDSOF_GX/issues/20). Proposed reviewer: Ricardo. These are design proposals, not accepted decisions and not stakeholder validation.
- **Inputs:** vision J1–J3 and §4.3; backlog US-01–US-11; the boundaries, asynchronous mechanism, identity and simulator proposed in EN-01-01 ([PR #30](https://github.com/Phenriquerafael/LABDSOF_GX/pull/30), unmerged at the time of writing). If those ADRs change, the affected sections of the design must be reviewed again.
- **DEC-10:** issue and intervention state machines with allow-listed transitions, optimistic versions and idempotency keys ([07 §3](../07-technical-design.md#3-states-and-business-rules)). Proposed rules to validate: approving a contractor's counterproposal counts as that contractor's acceptance; a cancelled issue is not reopened (a new report is made instead); rescheduling after a booking confirmation is done by cancelling or by manual booking confirmation; contractors cannot cancel after acceptance through the platform.
- **DEC-04 input:** Dispatch takes only the user identity from the token, checks contractor assignment from its own data and fetches administrator context from Issue for each command, failing closed ([07 §4.3](../07-technical-design.md#43-how-dispatch-obtains-trustworthy-authorisation-context)). Alternatives (token claims, read-only database access, membership projection) and their trade-offs are recorded there; projection versus per-request calls stays open for EN-01-03.
- **DEC-06 input:** calendar/simulator contract outline with outcome classification, reconciliation by request ID and a `discrepancy` state ([07 §4.5](../07-technical-design.md#45-calendarsimulator-contract-outline)).
- **DEC-07 input:** an “Enforced in” column and new rows in the [access matrix](../09-security-privacy.md#2-proposed-access-matrix); threat T-11. Whether contractor accounts are scoped to an organisation stays open.
- **EN01-U7 (from #30):** starting retry, lease and retention values proposed in [07 §4.6](../07-technical-design.md#46-retry-timeout-and-lease-values-proposal), to calibrate in the EN01-U2 spike.
- **Affected documents:** [technical design](../07-technical-design.md), [contract annex](../07a-contract-examples.md), [requirements](../requirements.md), [security](../09-security-privacy.md), [README](../../README.md).

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
| LIM-04 | No measured baseline for the primary metric; the current-process condition will be simulated | An effort reduction can be shown only in controlled tasks, not in real operation | Collect interview time estimates (DISC-01-02); freeze the protocol and threshold before EN-04 | Afonso (BA/PO) |

Add implementation debt when it exists; future features are not automatically technical debt.
