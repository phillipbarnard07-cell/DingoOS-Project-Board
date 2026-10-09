# DingoOS IP Production Design — Release Authorization Contract

**Date:** 2026-10-10  
**Private implementation:** [DingoOS PR #75](https://github.com/phillipbarnard07-cell/DingoOS/pull/75)  
**Workstream:** IP-03 → IP-07 protected-IP rights, provenance, eligibility and release governance  
**Status:** Design contract committed; authoritative integration and execution evidence remain blocked.

## Purpose

Formalize the production-critical boundary between IP-07 technical eligibility and consequential release authorization. This block continues the existing stack; it does not restart or replace prior work.

## Private-repository commits

- Contract: [`c77461e`](https://github.com/phillipbarnard07-cell/DingoOS/commit/c77461ecdfb6af427dbb38097c325c8437da06eb)
- Production design reassessment update: [`a3e93aa`](https://github.com/phillipbarnard07-cell/DingoOS/commit/a3e93aa0fde80517f3cb1bb68fa7bb3754d139cd)
- Binding production exit criteria update: [`82584ba`](https://github.com/phillipbarnard07-cell/DingoOS/commit/82584ba94b0ab2f71366c409295eafd1c350d395)

## What the contract formalizes

- Exact authorization scope: human principal, action, asset, artifact SHA-256, version, destination, release request, eligibility evidence, policy version, issue/expiry, and current revocation state.
- A three-phase protocol: prepare eligibility, authorize through an authoritative service, then immediately revalidate before release.
- Fail-closed handling for stale/unknown/revoked authorization, service outage, scope mismatch, issuer verification failure, replay, and provenance/checkpoint failure.
- Append-only audit semantics for grant, deny, revoke, expiry, retry, release failure, and uncertain outcome.
- Separation between proposer, human approver, governance authority, audit custody and release executor.
- A finite 16-case minimum conformance test suite and explicit implementation exit criteria.
- Reuse of existing canonical Authorization, IP-07, ProvenanceEvent and release-manifest contracts; no competing authorization authority is introduced.
- Existing `authorization_status: NOT_GRANTED` manifest invariant is preserved.
- No paid CI, subscription, network dependency installation or paid API is introduced.

## Source-level findings

The existing canonical `Authorization` primitive and IP-07's `ReleaseAuthorization` callback are useful integration seams, but neither alone proves that a decision came from an authoritative issuer, is currently unrevoked, and is scoped to the exact artifact and destination. A digest is not issuer authentication. A historical allow decision is not sufficient at dispatch time.

The contract therefore explicitly remains a design specification until the accepted integration baseline identifies the authoritative AuthorizationObject/identity/revocation service and its trust configuration. A local mock can support unit tests but cannot close the production integration gate.

## Verification status

- Design documents committed to the existing IP-07 branch.
- No runtime code or authorization service was implemented in this block.
- No tests/build/deployment were run by this documentation change.
- No legal rights or ownership conclusions are asserted.
- IP-03 through IP-07 remain stacked, open draft PRs; no merge or release was performed.

## Required next work

1. Reconcile the authoritative AuthorizationObject and identity/revocation contracts in the accepted system baseline.
2. Specify and review the concrete versioned decision schema and issuer-verification profile.
3. Implement a narrow adapter that retrieves trusted decisions and enforces scope, freshness, revocation and immediate pre-release revalidation.
4. Add the conformance cases in the contract, including failure injection and replay tests.
5. Execute the exact candidate on a clean local/self-hosted environment and retain source-bound evidence.
6. Independently review IP rights, trust/key custody, target recovery, package/build, and release manifest before any production decision.

**Decision: NOT PRODUCTION READY / NO-GO / HOLD.** Design formalization is progress, not production certification.
