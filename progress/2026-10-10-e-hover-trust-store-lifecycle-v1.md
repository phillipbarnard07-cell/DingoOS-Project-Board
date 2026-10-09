# E-HOVER-01 trust-store lifecycle block

Date: 2026-10-10
Status: Code and contract committed on DingoOS integration branch. Runtime tests not executed.

Added a runtime-injected HMAC trust-store policy:
- Unique key IDs, ACTIVE/REVOKED state, timezone-aware validity windows.
- Revocation overrides ACTIVE status; revoked tombstones never enter the keyring.
- Multiple active keys permit controlled rotation.
- Duplicate/malformed/expired/not-yet-valid/short-key snapshots fail closed; blocked snapshots return no keyring.
- Integrated entry point chains trust snapshot → signed envelope verification → content/freshness/register checks → screening-only sizing.

Authored tests: 12 trust-policy tests and 25 evidence/authentication test methods. These are not test-pass claims.

Production limitations: the policy library is not a key vault, trusted clock, revocation service, or live SEARM client. Production must source key records/revocation state from a protected control plane and provide audited rotation/revocation, trusted time, and independent review. The E-HOVER canonical parameter register remains unresolved. State remains BACKGROUND_ONLY / NOT_BUILT / NOT_AUTHORIZED. No paid CI/services added.

- [Trust policy](https://github.com/phillipbarnard07-cell/DingoOS/blob/integration/all-github-repositories/engineering/reference_plants/ehover01_trust_policy.py)
- [Integrated evidence gate](https://github.com/phillipbarnard07-cell/DingoOS/blob/integration/all-github-repositories/engineering/reference_plants/ehover01_searm_evidence.py)
- [Trust-policy tests](https://github.com/phillipbarnard07-cell/DingoOS/blob/integration/all-github-repositories/tests/test_ehover01_trust_policy.py)
- [Evidence tests](https://github.com/phillipbarnard07-cell/DingoOS/blob/integration/all-github-repositories/tests/test_ehover01_searm_evidence.py)
- [Contract](https://github.com/phillipbarnard07-cell/DingoOS/blob/integration/all-github-repositories/docs/advanced-systems/E-HOVER-01-TRUST-STORE-LIFECYCLE-V1.md)
- [Detailed report](https://github.com/phillipbarnard07-cell/DingoOS/blob/integration/all-github-repositories/progress/2026-10-10-e-hover-trust-store-lifecycle-v1.md)
