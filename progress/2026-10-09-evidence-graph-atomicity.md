# Evidence Graph relationship atomicity

**Date:** 2026-10-09  
**Status:** Implementation and architecture contract pushed; tests authored but not executed.

## Delivered

- Changed `EvidenceGraphRepository.relate` to validate endpoint semantics before writing and atomically append missing endpoint nodes plus the edge using `ResearchLedger.append_batch`.
- Prevented invalid governed relationships from leaving orphan endpoint nodes.
- Changed `ResearchService.register_resource` to persist the resource before publishing it to the in-memory registry, with duplicate-ID preflight.
- Added `ResearchLedger.append_batch_with_provenance`, which evaluates provenance factories while holding the append lock and binds the provenance envelope to the actual preceding ledger hash.
- Updated the no-provenance `ResearchService.relate_evidence` path to atomically commit its provenance anchor, missing graph nodes and relationship edge; injected batch failure leaves no orphan anchor.
- Added `tests/evidence/test_graph_repository_atomicity.py` (3 test functions): successful batch, injected write failure, and invalid endpoint type.
- Added `tests/core/test_research_service_atomicity.py` (4 test functions): resource persistence failure, duplicate resource handling, anchor-plus-edge atomic commit, and no orphan anchor on injected failure.
- Formalized the contract in `docs/architecture/EVIDENCE_GRAPH_RELATIONSHIP_ATOMICITY_V1.md`.

## Scope and limitation

The graph node/edge batch and the no-provenance convenience path's provenance anchor are atomic. Callers that supply a pre-existing provenance ID remain responsible for that external record's lifecycle; the relationship batch itself still commits all missing nodes and its edge together. No claim of system-wide or distributed transaction support is made.

## Verification

Files were pushed to `integration/all-github-repositories` and fetched back from GitHub to confirm presence. Tests are authored but not executed in this session. No GitHub Actions or paid services were used.

## Next block

Audit remaining append-with-provenance callers for similar multi-record gaps; then validate the focused ledger, graph, resource and SEARM tests in an executable checkout.
