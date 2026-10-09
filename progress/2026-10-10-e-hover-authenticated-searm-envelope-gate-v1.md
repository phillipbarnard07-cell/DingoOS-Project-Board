# E-HOVER-01 authenticated SEARM envelope gate

Date: 2026-10-10
Status: Authenticated-envelope gate committed to the DingoOS integration branch; runtime tests not executed.

The evidence resolver now verifies HMAC-SHA256 signatures over the complete canonical evidence envelope (all metadata plus content digest, excluding only the signature itself), using a caller-supplied trusted key store. Unknown key IDs, missing/short keys, malformed signatures and metadata/content tampering block before sizing. The integrated authenticated entry point only continues to existing evidence-integrity/freshness checks after successful authentication.

Eight authenticated-envelope regression tests were added; 22 test methods are authored in total. They have not been run; no passing test claim is made.

Trust boundary: no keys are committed. Production must provide protected key provisioning, access control, rotation/revocation, and a trusted clock. HMAC is symmetric and not non-repudiable; asymmetric signatures may be needed for independent publishers. Live SEARM retrieval, durable revocation/audit, reviewer authorization, independent engineering qualification and runtime verification remain open. The canonical E-HOVER register is still unresolved. Status remains BACKGROUND_ONLY / NOT_BUILT / NOT_AUTHORIZED; this is not a production-readiness or certification claim. No paid CI/service added.

- Source: https://github.com/phillipbarnard07-cell/DingoOS/blob/integration/all-github-repositories/engineering/reference_plants/ehover01_searm_evidence.py
- Tests: https://github.com/phillipbarnard07-cell/DingoOS/blob/integration/all-github-repositories/tests/test_ehover01_searm_evidence.py
- Contract: https://github.com/phillipbarnard07-cell/DingoOS/blob/integration/all-github-repositories/docs/advanced-systems/E-HOVER-01-SEARM-EVIDENCE-BUNDLE-RESOLUTION-V1.md
- Detailed report: https://github.com/phillipbarnard07-cell/DingoOS/blob/integration/all-github-repositories/progress/2026-10-10-e-hover-authenticated-searm-envelope-gate-v1.md
