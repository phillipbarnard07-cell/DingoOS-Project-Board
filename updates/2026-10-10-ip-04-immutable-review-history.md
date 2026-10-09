# Engineering update — 2026-10-10: IP-04

**Workstream:** DingoOS Pty Ltd protected-IP governance  
**State:** Reference implementation pushed; canonical runtime integration and execution verification pending  
**Private implementation PR:** [DingoOS #72](https://github.com/phillipbarnard07-cell/DingoOS/pull/72)  
**Dependency:** [IP-03 rights-review ledger, PR #71](https://github.com/phillipbarnard07-cell/DingoOS/pull/71)

## Delivered

- Deterministic review events with canonical JSON and SHA-256 event chaining.
- Explicit authorization grant scope for actor, asset, domain, and action.
- Fail-closed rejection for denied or mismatched authorization.
- Duplicate event-ID rejection, prior-hash continuity checks, tamper detection, and revocation-state constraints.
- Seven standard-library regression tests authored.
- Formal specification documenting trust limits, private evidence handling, and canonical integration requirements.

## Status distinctions

| Gate | Status |
|---|---|
| Source and test files committed | DONE |
| Draft PR opened | DONE |
| Tests authored | DONE |
| Tests executed on exact canonical checkout | NOT VERIFIED |
| Durable append-only storage integration | NOT STARTED |
| Canonical AuthorizationObject/ProvenanceEvent integration | NOT STARTED |
| Release authorization | HOLD |

## Safety and trust boundary

The implementation is an in-memory reference log. A hash chain is not a digital signature, identity proof, trusted timestamp, or legal proof. AuthorizationGrant must be validated by the authoritative DingoOS authorization service before production integration. An independently anchored chain head and durable append-only storage are required for stronger tamper evidence.

No legal-rights conclusion, production certification, or release authorization is asserted. No paid GitHub service or API was introduced.

## Verification next step

Run the IP-04 and IP-03 unit tests against the exact proposed revision, preserve output and revision digest, then integrate with the canonical authorization and provenance services. Keep merge/release HOLD until those gates have evidence.
