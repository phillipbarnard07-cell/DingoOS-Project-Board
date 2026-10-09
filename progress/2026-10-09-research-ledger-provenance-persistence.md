# DingoOS progress — ResearchLedger canonical provenance persistence (2026-10-09)

## Reassessment finding

The existing `ResearchLedger` already exposes `append_provenance_event` and `get_provenance_event`. The append path was present, but canonical `ProvenanceEvent.as_record()` omitted the event's own `provenance_hash`, while reconstruction required that field. This prevented the intended serialize/persist/read/verify round trip. The right fix is to correct the canonical serializer and retain the existing ledger as the only persistence authority—not add a parallel ledger or route around the existing append boundary.

## Changes committed on `integration/all-github-repositories`

- `core/provenance.py`: `ProvenanceEvent.as_record()` now includes `provenance_hash` after verifying the event. The event digest still covers only its unsigned canonical payload.
- `tests/core/test_research_ledger_provenance.py`: 8 focused regression tests for round-trip reconstruction, identical replay, divergent event-ID collision, invalid-event rejection, inner-hash tampering despite a recomputed outer hash, ledger sequence integrity, separate inner/outer hashes, and invalid-event serialization.
- `docs/architecture/RESEARCH_LEDGER_PROVENANCE_PERSISTENCE_V1.md`: records the contract, trust boundaries, local test command and next dependency.

## Verification boundary

The three files were re-fetched from GitHub; the serializer patch and eight test functions were confirmed present. Tests have **not** been executed in this session. No runtime or passing-test claim is made.

Run in a real checkout:

```bash
python -m pytest tests/core/test_research_ledger_provenance.py -q
```

No paid CI or new dependency was introduced.

## Acceptance boundary

A passing local test suite is necessary but not sufficient. Next, connect and test actual SEARM/ResearchLedger call sites so material state transitions emit canonical events. Preserve the distinction between event hash and ledger chain hash; no event persistence by itself promotes epistemic state, establishes truth, qualifies evidence, or authorizes consequential action.
