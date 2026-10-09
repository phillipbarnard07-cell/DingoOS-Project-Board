# Evidence Graph relationship atomicity

**Date:** 2026-10-09  
**Status:** Implementation and architecture contract pushed; tests authored but not executed.

## Delivered

- Changed `EvidenceGraphRepository.relate` to validate endpoint semantics before writing and atomically append missing endpoint nodes plus the edge using `ResearchLedger.append_batch`.
- Prevented invalid governed relationships from leaving orphan endpoint nodes.
- Changed `ResearchService.register_resource` to persist the resource before publishing it to the in-memory registry, with duplicate-ID preflight.
- Added `tests/evidence/test_graph_repository_atomicity.py` (3 test functions): successful batch, injected write failure, and invalid endpoint type.
- Added `tests/core/test_research_service_atomicity.py` (2 test functions): resource persistence failure and duplicate resource handling.
- Formalized the contract in `docs/architecture/EVIDENCE_GRAPH_RELATIONSHIP_ATOMICITY_V1.md`.

## Scope and limitation

The graph node/edge batch is atomic. The legacy ResearchService convenience method may still write a separate provenance anchor before graph relation persistence; a subsequent graph failure can leave an unused anchor. This is explicitly documented and is the next narrow follow-up if its callers need full anchor-plus-edge atomicity. No claim of system-wide or distributed transaction support is made.

## Verification

Files were pushed to `integration/all-github-repositories` and fetched back from GitHub to confirm presence. Tests are authored but not executed in this session. No GitHub Actions or paid services were used.

## Next block

Assess whether the convenience relationship API can construct its provenance envelope and graph records under one ledger transaction without bypassing `append_with_provenance`'s locking and previous-hash contract. Then validate the focused tests in an executable checkout.
