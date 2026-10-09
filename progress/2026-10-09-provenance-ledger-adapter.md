# DingoOS progress — canonical provenance/ledger adapter (2026-10-09)

## Scope delivered

Implemented the next non-restart integration block against the existing `core.provenance` types on DingoOS branch `integration/all-github-repositories`.

- `core/provenance_adapter.py`: maps a verified canonical `ProvenanceEvent` to the existing chained `ProvenanceRecord` envelope.
- `tests/core/test_provenance_adapter.py`: 9 targeted regression tests covering valid conversion, lineage retention, separate hashes, invalid event rejection, required repository/revision, malformed predecessor digest, changed event payload, a rehashed-but-divergent ledger payload, and invalid binding types.
- `docs/architecture/PROVENANCE_EVENT_LEDGER_ADAPTER_V1.md`: defines mapping, trust boundary, usage, limits and local test command.

## Contract decisions

1. Canonical event `provenance_hash` and ledger chain `provenance_hash` are distinct hashes with distinct meanings. The event hash is retained under `canonical_event_provenance_hash`; the ledger hash remains the existing chained record hash.
2. Event/object/version/content identities and parent/source lineage are preserved.
3. Binding verification compares canonical payload fields as well as identity, event hash, and lineage. A ledger record rehashed after payload divergence must still fail event binding.
4. The adapter is pure conversion/validation. It does not persist records, create a parallel ledger, authorize action, promote epistemic state, or replace SEARM.
5. Repository and revision must be present. Optional resource qualification is transported as metadata and is not independently certified by this adapter.

## Verification status

- Files were committed through the GitHub contents API and are to be re-fetched for source-presence confirmation.
- Tests are **authored, not executed** in this session. No passing result is claimed.
- No paid CI, dependency, API or subscription was added.

## Next block

Run `python -m pytest tests/core/test_provenance_adapter.py -q` in a real local checkout. Then integrate the adapter at the existing authorized ResearchLedger persistence boundary and test append-only sequence behavior end-to-end. Only after that proceed to canonical C-4PO EvaluationObject and SEARM qualification adapters.
