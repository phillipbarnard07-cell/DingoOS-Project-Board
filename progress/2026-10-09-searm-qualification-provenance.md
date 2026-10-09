# DingoOS progress — SEARM qualification transition provenance boundary (2026-10-09)

## Reassessment

The repository has an existing epistemic state machine (`evidence/epistemic.py`) and an append-only `ResearchLedger`, but the inspected paths did not expose an authoritative `searm_kernel.py`/qualification service. This block therefore does not claim end-to-end SEARM integration. It supplies a narrow provenance adapter for a decision made by the authoritative SEARM workflow once that call site is identified.

## Changes pushed on `integration/all-github-repositories`

- `core/searm_qualification_provenance.py`: validates legal state transitions, decision ID and SHA-256 digest shape, required evidence and qualification-basis references; canonicalizes lineage references; persists a canonical `SEARM_EPISTEMIC_TRANSITION_RECORDED` event through the existing ledger.
- Stable event identity uses object ID + decision ID, so divergent replay under the same decision identity collides and fails closed rather than generating a second event.
- `tests/core/test_searm_qualification_provenance.py`: tests lineage binding, idempotent replay, divergent replay rejection, illegal transitions, missing references, malformed digest and canonical reference order.
- `docs/architecture/SEARM_QUALIFICATION_TRANSITION_PROVENANCE_V1.md`: documents contract, limits and next dependency.

## Authority boundary

This adapter records a decision; it does not make the qualification decision, infer qualification from the existence of IDs/hashes, mutate epistemic state, establish truth or grant consequential authority. The actual SEARM call site must be located and integrated before calling this end-to-end.

## Verification

Source, tests and contract were pushed via GitHub contents API. Runtime tests have not been executed in this session. Run:

```bash
python -m pytest tests/core/test_searm_qualification_provenance.py tests/core/test_research_ledger_provenance.py -q
```

No paid CI or new dependency was added.
