# ADR-005 — Own authentication and membership-based authorisation

**Status:** Proposed
**Date:** 2026-10-15
**Participants:** Ricardo (P2, Tech Lead) — proposer; team review pending; EN-01-04 security review required
**Related decision:** DEC-04; DEC-07 (visibility and data lifecycle)
**Supersedes / superseded by:** —

## Context

§6.6 requires appropriate identity and access controls; its footnote allows mocked authentication for the MVP (E31). NFR-01 requires the author/admin/contractor/other-building matrix to be enforced and tested in the backend; the [access matrix](../09-security-privacy.md#2-proposed-access-matrix) defines the policy. The team prefers a small, real login over both a mock and a separate identity provider.

## Alternatives

| Option | Benefits | Costs/risks | Evidence |
|---|---|---|---|
| **A. Own login in the Issue service, argon2id, JWT in httpOnly cookies, membership RBAC** | Real authentication with no extra container; the team controls the claims used by authorisation | The team implements security-sensitive code; must be reviewed in EN-01-04 | E10, E06 |
| B. Mocked authentication (seeded users, no passwords) | Least effort; allowed by the footnote | Weaker demonstration of §6.6; still needs real authorisation | E31 §6.6 |
| C. Keycloak | Full OIDC/SAML | Base memory of 1250 MB per instance; configuration and operational load | E29 |
| D. Zitadel | Modern OIDC server | AGPL-3.0 licence; extra containers and configuration | E29 |

## Proposed or accepted decision

**A**, proposed, not accepted:

- **Accounts:** the Issue service owns users, credentials and memberships (user × building × role). Accounts are seeded or created by invitation from an administrator. No self-service sign-up, email password reset or MFA in the MVP.
- **Passwords:** argon2id with explicitly configured parameters (`Bun.password`, E10); parameters chosen in EN-01-04.
- **Single origin:** nginx serves the SPA and proxies both APIs, so there is no CORS.
- **Access token:** short-lived JWT (about 15 minutes) signed by Issue with an asymmetric key (ES256 or EdDSA), sent as an httpOnly, Secure, SameSite=Strict cookie; never stored in localStorage. Claims: `sub`, `iss`, `aud`, `exp`, `iat`, `jti`; memberships are resolved server-side, not trusted from the client.
- **Refresh token:** opaque random value, stored hashed in the Issue database, rotated on each use, revocable; also in an httpOnly cookie.
- **Verification in Dispatch:** local verification with Issue's public key.
- **Service-to-service:** short-lived service JWTs with per-service keys and an `aud` claim for internal endpoints (worker → Issue, Issue ↔ Dispatch); no shared superuser credential.
- **Authorisation:** enforced in each service for every operation, using building membership and resource assignment; administrator actions are audited. Hiding UI elements is never the control.

## Consequences

- Authentication is real but small; [security assessment](../09-security-privacy.md) wording on mocked authentication changes accordingly.
- Security-sensitive code (hashing, token signing, cookie flags, rotation, CSRF assumptions under SameSite=Strict) needs an explicit EN-01-04 review and negative tests.
- Account recovery is manual (administrator re-invites) in the MVP.
- Swap path: an OIDC provider can later issue tokens with the same claims; authorisation logic does not change.

## Verification and review

EN-01-04 reviews the design; tests cover the full access matrix, expired/forged/replayed tokens, refresh rotation and revocation, and service tokens with the wrong audience. Revisit before any real-user pilot.

## Record

2026-10-15 — proposed by Ricardo (Tech Lead) after a structured design interview for EN-01-01; drafting and source checks AI-assisted. Evidence: [evidence register](README.md#evidence-register-en-01-01) E06, E10, E29, E31; issue [#19](https://github.com/Phenriquerafael/LABDSOF_GX/issues/19); EN-01-01 pull request. Affected documents: [security](../09-security-privacy.md), [technical design](../08-technical-design.md), DEC-04. Final decision, date and participants: pending team review.
