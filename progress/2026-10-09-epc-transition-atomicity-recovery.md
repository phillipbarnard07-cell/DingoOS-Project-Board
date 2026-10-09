# EPC transition atomicity and recovery

**Date:** 2026-10-09  
**Status:** Implementation and contract pushed; tests authored but not executed in this session.

## Delivered

- Added `ResearchLedger.append_batch`, an atomic append-only ledger transaction that allows repeated object IDs for immutable projection revisions without changing the stricter idempotent-batch API.
- Updated `EpistemicService._transition` to persist the EPC lifecycle event and claim graph projection in one batch, then mutate in-memory state only after durable commit.
- Added `EpistemicService.refute_with_provenance` to validate the canonical SEARM transition event and atomically commit the EPC state event, claim graph status, evidence graph node, `FALSIFIES` edge, and canonical qualification provenance event.
- Wired `SEARMResearchService.refute_claim` to prepare the canonical event before the state mutation and use the atomic path.
- Added ledger tests for ordered hash-chain records, failed atomic replacement, and repeated projection IDs.
- Added EPC tests for injected write failure and restart recovery.
- Expanded SEARM integration tests to verify canonical event binding, the persisted falsification edge, graph verification, and absence of partial governed records after injected failure.
- Formalized the behavior and limitations in `docs/architecture/EPC_TRANSITION_ATOMICITY_AND_RECOVERY_V1.md`.

## Important scope note

The SEARM computation/refutation result (and any counter-hypothesis) may be persisted before the governed transition transaction. If the transaction fails, those calculation records remain auditable, but the EPC `REFUTED` state, corresponding graph projection, falsification edge and qualification event are not committed. Claim registration and non-transition event writers remain a separate atomicity follow-up.

## Verification

Source, tests and contracts were pushed through the GitHub contents API and then fetched to confirm their presence. Tests were authored but not executed; no passing runtime test or Actions claim is made. No paid CI gate, new dependency, subscription or external API was introduced.

## Next block

Audit claim registration and other `EpistemicService` event writers for append-before-mutate behavior; then validate the focused ledger/EPC/SEARM/API tests in an executable checkout and fix any observed failures before merge.
