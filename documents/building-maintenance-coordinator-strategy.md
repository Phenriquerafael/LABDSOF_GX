# Building Maintenance Coordinator — Strategy

**Status:** proposal v0.2 · **Current stage:** Sprint 1 · **Reviewed:** 2026-10-07

This document summarises the product direction. Detailed scope belongs in the [vision](04-product-vision.md), priorities in the [backlog](06-product-backlog.md) and open choices in the [decision register](planning/decisions-and-feedback.md). This documentation revision does not represent team or lecturer approval.

## 1. Problem and positioning

**[Hypothesis]** Reports scattered across calls, messages and emails increase the effort of coordinating faults in some condominiums. Frequency, impact and alternatives already in use need investigation.

> BMC helps condominium administrators turn fault reports into tracked interventions, reducing coordination work and keeping residents informed.

The target community is residential condominiums in Portugal. Faults affecting water, electricity, lifts or shared spaces disrupt everyday life and align the product with the resilience challenge.

| Role | Expected benefit [Hypothesis] |
|---|---|
| Professional administrator — proposed primary segment | Less effort per issue; visible responsibility and status |
| Volunteer administrator — secondary segment | Simpler coordination |
| Resident/unit owner | Report and track without repeated contact |
| Maintenance contractor | Receive clear requests, respond and report completion |

Willingness to pay and buyer identity are unvalidated. Interviews with volunteers do not replace evidence from the professional segment. SaaS per building/unit is a commercial hypothesis (DEC-02).

## 2. Provisional scope baseline

**Report → suggest triage → administrator review → propose intervention → approval → contractor response → scheduling → execution → closure and history.**

- Text reports and synthetic demonstration data.
- AI suggests classification; rules and manual triage allow continued operation without AI.
- Duplicate suggestions, when implemented, are reviewed and preserve original reports.
- Small contractor catalogue; explainable rule-based ranking, without a marketplace.
- Approval authorises the request; a booking is confirmed only after contractor acceptance and calendar registration, or explicit manual confirmation.
- One notification channel, “This affects me too” participation, history and optional basic cost.
- The administrator closes the issue after contractor-reported completion, or with recorded justification.

**Sprint 1:** discovery, decisions and design. The evaluator clarified that the walking skeleton is optional; the team decided not to develop it this sprint (DEC-11, recorded on 2026-10-10). **Sprint 2:** first operational version of the cycle. **Sprint 3:** complete, evaluate and strengthen the same MVP.

Photos, AI visual analysis, cost charts and narrative summaries are **post-MVP** candidates, without a Sprint 3 commitment. Automatic expense approval is outside the baseline. IoT is an open alternative (DEC-01), not an automatically added requirement.

## 3. Value and measurement

Proposed primary metric: **the administrator's active time to triage and prepare an intervention**, comparing equivalent tasks. Time until dispatch, acceptance and resolution are separate metrics; resolution also depends on external factors.

Classification quality, duplicate-linking errors, latency and cost are evaluated in the [AI plan](07-responsible-ai-assessment.md). Targets are not results. Perfect detection of critical situations is not promised.

## 4. AI and decision safety

| Capability | Proposal |
|---|---|
| Classify text | Meaningful AI workflow, compared against a rule-based baseline and with manual fallback |
| Critical signals | Rules independent of AI and highlighting for review; rules also have limitations |
| Duplicates | Structured context first; embeddings depend on evaluation (DEC-05) |
| Contractors | Rule-based filters and ranking; explanations derived from criteria |
| Approve/schedule | Permissions and transitions enforced by the backend; the model does not perform these actions |
| Costs | Structured records; analytics and narratives post-MVP |

The assignment requires a non-AI baseline **or** fallback. We propose both to evaluate value and allow continuity. Versioned model instructions complement output validation and code controls; they do not replace them.

## 5. Proposed technical direction

Two independently deployable backend components: **Issue Service** (issues, triage and history) and **Dispatch Service** (contractors and interventions), with asynchronous processing. Stack, asynchronous mechanism and deployment are proposed in [ADR-001–006](adr/README.md) (DEC-04, team review pending).

Proposed integration: a calendar with a realistic simulator as the initial planning option; provider and final implementation in DEC-06. A modular monolith requires justification and lecturer acceptance; this exception has not already been granted.

Mock authentication is permitted for the MVP; the team proposes a small own login instead ([ADR-005](adr/ADR-005-authentication-authorisation.md)). Authorisation by role, building and intervention remains enforced and tested. See [security](09-security-privacy.md).

## 6. Execution

- [Sprint 1 phases](planning/sprint-1-plan.md): exit criteria and evidence.
- [Team working agreement](governance/team-working-agreement.md): named roles agreed; methodology and remaining setup await ratification.
- [DoR](governance/definition-of-ready.md) and [DoD](governance/definition-of-done.md): operational proposals.
- [Git workflow](governance/git-workflow.md): collaboration, reviews and traceability.
- [Decisions and feedback](planning/decisions-and-feedback.md): justified choices and changes.

Immediate priority: assign work items to the agreed team roles, gather evidence, review the proposal with stakeholders and consolidate the technical design for later implementation. Status in the [README](../README.md).

