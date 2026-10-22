# [EN-01-02] Consolidate the domain model and contract outlines

- Publication: Published as [#20](https://github.com/Phenriquerafael/LABDSOF_GX/issues/20) (checked 2026-10-19); GitHub owns live status.
- Workflow: Backlog; proposed milestone: S1 — Discover, Validate and Design; dates/estimate: to be agreed.
- Type: task; suggested labels: `type:task`, `area:backend`, `phase:3`, `priority:must`.
- Parent: [EN-01](EN-01-technical-design.md); epic: [EP-04](../epics/EP-04-engineering-and-quality.md).
- Allocation: see the [backlog README](../README.md).
- Source: [Assignment](../../../LABDSOF-26-27-Assignment.md) §6.2–§6.6; §7.1.2 item 7.
- Dependencies for completion: [DISC-03-03](DISC-03-03-refine-product-vision.md), [EN-01-01](EN-01-01-select-stack-and-boundaries.md)
- Related decisions: DEC-04, DEC-06, DEC-07, DEC-10, in the [register](../../planning/decisions-and-feedback.md).

## Objective and scope

Specify consistent business states and interfaces for later implementation.

Existing drafts are inputs to review and improve; do not recreate work already supported by evidence. Preparation may overlap predecessor work; confirm dependent conclusions only after the needed findings/decisions exist.

Excluded: Production-complete OpenAPI, migrations and executable integration tests.

## Acceptance criteria

- [ ] Review entities, relationships, data ownership and building membership against the agreed journeys.
- [ ] Specify approval, contractor response, scheduling, completion, reopening and cancellation rules, including invalid/concurrent transitions.
- [ ] Provide representative request/response and event examples, identifiers, versions and error behaviour for the principal journey.
- [ ] Outline the realistic calendar/simulator contract, delayed/invalid/uncertain outcomes and reconciliation/idempotency requirements.
- [ ] Explain how Dispatch obtains trustworthy authorisation context without cross-writing Issue data; link unresolved details to decisions.

## Deliverables and evidence

- [Technical design](../../07-technical-design.md) — update the relevant sections and link the change/review evidence.
- [Requirements](../../requirements.md) — update the relevant sections and link the change/review evidence.
- [Security access matrix](../../09-security-privacy.md) — update the relevant sections and link the change/review evidence.

## Verification

Reviewer walks one successful journey and failure/denied-access scenarios through the model and contracts.

Use the documentation/research checks appropriate to this task; implementation tests are not required for a design-only outcome.

## Completion record

- Result/findings and limitations: domain model reviewed against J1–J3 on 2026-10-19 by Afonso, with one writing service per entity and six gaps closed (areas, contractor account link, proposal versions, triage decision, completion report versus cost, history projection). Issue and intervention state machines, invalid and concurrent transition rules, API and internal operations, event catalogue, Dispatch authorisation context, calendar/simulator contract and starting retry values drafted in [07 §2–§4](../../07-technical-design.md#2-domain-model-and-data-ownership); examples and a 16-scenario review walkthrough in the [contract annex](../../07a-contract-examples.md). Limitations: builds on EN-01-01 proposals in the unmerged PR #30; rules are not validated with administrators or contractors (DEC-10); retry values are unmeasured; no executable OpenAPI (excluded).
- Evidence/PR/review links: the pull request referencing #20; review by the assigned reviewer pending, including the walkthrough. Document presence alone is not completion evidence.
- Decision/requirement changes: DEC-10 status and the EN-01-02 entry in the [register](../../planning/decisions-and-feedback.md#domain-and-contract-proposals--en-01-02); NFR-01/04/11 criteria and the DEC-10 open decision in [requirements](../../requirements.md); access matrix and T-11 in [security](../../09-security-privacy.md).
- [ ] DoR reviewed with date, estimate, owner and reviewer confirmed.
- [ ] Acceptance criteria and applicable [DoD](../../governance/definition-of-done.md) verified with evidence.
