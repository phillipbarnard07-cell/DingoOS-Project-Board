# DingoOS progress — independent replication atomicity

**Date:** 2026-10-09  
**Implementation branch:** `integration/all-github-repositories`  
**Status:** Implementation and regression tests committed; tests not executed.

## Reassessment finding

`ResearchService.persist_replication` persisted a `REPLICATION` record and then wrote its required `REPLICATES` graph edge in a separate operation. A failure between those writes could leave a durable replication with incomplete graph lineage.

The audit also found that `ResearchService` read `ProvenanceRecord.integrity_hash`, while the canonical dataclass exposes `provenance_hash`. The service now uses the real canonical field, preserving the existing serialized envelope key.

## Delivered

- Added `EvidenceGraphRepository.relate_with_primary_record` to commit a provenance-bearing primary record, missing graph nodes and its governed edge in one batch.
- Changed new independent replication persistence to use that atomic operation.
- Kept preconditions for source claim binding, independent operator/resources/provenance, evidence references and inherited security classification.
- Added replay behavior: identical replay returns the existing record; divergent reuse of an ID fails closed.
- Preserved a narrow legacy recovery path for an existing replication whose required edge is absent.
- Added `tests/core/test_replication_atomicity.py` covering atomic success, injected file-replacement failure, identical replay and divergent replay.
- Formalized the contract in `docs/architecture/REPLICATION_RELATIONSHIP_ATOMICITY_V1.md`.

## Verification boundary

The changed files have been committed and fetched back from GitHub. Tests have **not** been executed in this environment. No GitHub Actions run or paid service was used.

## Next audit items

1. Inspect resource qualification for persist-before-mutate and recovery consistency.
2. Review any remaining multi-record graph projections and legacy partial-state handling.
3. Run focused replication, graph and provenance tests in an executable checkout, then the full suite before calling this verified.

**Evidence Over Assumption | Science Over Belief | Provenance Over Assertion.**
