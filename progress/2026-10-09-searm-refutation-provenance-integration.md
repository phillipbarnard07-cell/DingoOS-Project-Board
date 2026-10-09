# SEARM refutation-to-provenance integration

**Date:** 2026-10-09  
**Status:** Implementation, contract and regression tests pushed. Runtime tests not executed in this session.

## Delivered

- Wired the existing `SEARMResearchService.refute_claim` path to the canonical qualification-provenance adapter.
- Bound the decision digest to the claim, pre-transition EPC event sequence, transition states, deterministic SEARM refutation result, evidence IDs, and persisted evidence/refutation provenance IDs.
- Compute and validate the decision digest before the EPC state mutation.
- Persist `SEARM_EPISTEMIC_TRANSITION_RECORDED` through the existing Research Ledger.
- Return the provenance ledger entry as `PersistedRefutation.qualification_entry`.
- Added end-to-end regression tests for contradictory results and non-contradictory results that must not emit a qualification event.
- Documented the authority split between SEARM refutation, EPC state transition, and provenance recording.

## Files

- `core/searm_service.py`
- `tests/core/test_searm_service_provenance.py`
- `docs/architecture/SEARM_REFUTATION_TO_PROVENANCE_INTEGRATION_V1.md`
- Updated `docs/architecture/SEARM_QUALIFICATION_TRANSITION_PROVENANCE_V1.md`

## Boundaries

The existing SEARM kernel determines whether the observation contradicts the prediction; EPC owns the epistemic state transition; the adapter records the decision and lineage. This is not a truth oracle and does not grant consequential authority. The canonical event currently has no parent event IDs because EPC lifecycle records do not yet expose canonical provenance-event IDs; the pre-transition ledger sequence is bound as the version identifier instead.

## Verification

Tests are authored but not executed in this session. No claim of passing runtime tests or GitHub Actions is made. No paid CI, external dependency, or subscription was introduced.

## Next block

Run the focused tests in a real checkout; inspect and address any failures; then extend the same audit to other epistemic transitions and ensure no alternate public API path can bypass governed lifecycle rules.
