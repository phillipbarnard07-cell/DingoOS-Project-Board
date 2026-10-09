# SEARM decision digest binding hardening

**Date:** 2026-10-09  
**Status:** Written and pushed; regression tests authored, runtime execution not observed.

## Problem found

The SEARM qualification provenance boundary previously checked that `decision_digest` looked like a SHA-256 digest, but did not prove it represented the transition decision and its evidence/qualification lineage. A well-formed hash of unrelated content could therefore be recorded as the decision digest.

## Change delivered

- Added `qualification_decision_digest` to compute a deterministic digest over the canonical decision payload.
- Bound the digest to object ID, version ID, source/target epistemic states, decision ID, sorted/deduplicated evidence IDs, qualification-basis IDs, optional evaluation ID, and parent event IDs.
- Made `record_qualification_transition` recompute the digest and reject mismatches before writing to the ledger.
- Preserved deterministic event identity and fail-closed collision behavior.
- Added regression coverage for unrelated/tampered digest rejection and lineage-sensitive digest changes.
- Updated the architecture contract.

## Scope and authority

This hardens integrity at the provenance boundary; it does not create the missing authoritative SEARM qualification decision-maker. Evidence references remain references, not proof of evidence quality. No truth claim, state mutation, or consequential authorization is granted.

## Verification

Test file: `tests/core/test_searm_qualification_provenance.py`. Tests are authored but were not executed in this session. No paid CI, subscription, or external dependency was introduced.

## Next block

Identify the actual SEARM request/qualification call site and wire its computed decision payload into this boundary. Add an end-to-end request-to-ledger replay test before claiming full SEARM integration.
