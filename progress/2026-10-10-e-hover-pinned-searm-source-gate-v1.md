# DingoOS — pinned SEARM source gate

Date: 2026-10-10
Canonical branch: `integration/all-github-repositories`

## Block recorded

The canonical DingoOS repository now contains a reference source gate that requires:
- explicit repository allowlisting;
- a full immutable Git commit SHA;
- a normalized repository-relative JSON path;
- an independently verified source-descriptor signature callback;
- confirmation of the resolved commit SHA;
- matching Git blob SHA and raw-content SHA-256;
- a non-empty, structurally valid evidence bundle;
- subsequent use of the existing trust-policy, HMAC, freshness, register-binding and screening-sizing gates.

Code: [pinned SEARM source resolver](https://github.com/phillipbarnard07-cell/DingoOS/blob/integration/all-github-repositories/engineering/reference_plants/ehover01_searm_source.py)

Contract: [source resolution contract](https://github.com/phillipbarnard07-cell/DingoOS/blob/integration/all-github-repositories/docs/advanced-systems/E-HOVER-01-SEARM-SOURCE-RESOLUTION-V1.md)

Tests: [synthetic regression tests](https://github.com/phillipbarnard07-cell/DingoOS/blob/integration/all-github-repositories/tests/test_ehover01_searm_source.py)

## Honest verification status

The 16 test methods are authored but have NOT been executed. No live SEARM source client, trusted signature root, or production integration is claimed. The fetcher and manifest verifier are injected security-critical dependencies; a permissive verifier invalidates the authentication boundary. Current state remains BACKGROUND_ONLY / NOT_BUILT / NOT_AUTHORIZED. No vehicle parameter is promoted to qualified truth and no operational activity is authorized.

A malformed-allowlist review found and corrected a potential type-error path before finalizing this block. Regression tests were added for absent and empty allowlists.

No paid CI, hosted-runner spend, subscription, or paid API was introduced.

Evidence Over Assumption | Science Over Belief | Provenance Over Assertion.
