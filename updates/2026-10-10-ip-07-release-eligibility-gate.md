# Engineering update — 2026-10-10: IP-07 pending-work review

**Workstream:** DingoOS protected-IP governance and provenance  
**Private implementation PR:** https://github.com/phillipbarnard07-cell/DingoOS/pull/75  
**Latest source revision:** `7f444930ec373834c057f1dca7a7af35c061407f`  
**State:** Canonical provenance shape, revision/destination binding, controlled register-source loader, fail-closed review-validity seam, and package-discovery correction implemented; production adapters and execution evidence pending

## New findings and corrections

The IP-07 pending review inspected the actual schemas on the IP-06 dependency branch:

- `schemas/provenance/provenance-event.schema.json`
- `schemas/ip-asset-register-v1.schema.json`
- `schemas/ip-release-manifest-v1.schema.json`
- `security/ip_policy.py`
- `security/integrity.py`

IP-07 has been corrected from its earlier custom envelope to the canonical ProvenanceEvent field shape (`event_id`, `object_id`, `version_id`, `content_hash`, `provenance_hash`, `event_type`, plus only permitted optional fields). A standard-library validator checks required/allowed fields, types, digest formats, and the canonical event hash. The test asset ID was changed to match the canonical `DGO-IP-...` format.

The canonical release-manifest schema requires `authorization_status: NOT_GRANTED`. It must not be treated as a grant or altered to imply release approval. The canonical asset register is now a required input. The gate calls the repository-maintained `scripts.validate_ip_register.validate_register` structural validator on each evaluation, checks the requested asset's release-relevant review states, requires an exact lowercase artifact digest, and applies the existing trusted-destination/classification policy. This structural validator is not a standards-complete JSON Schema engine. The new controlled-source loader is implemented in reference code, but production deployment must still protect the trusted root and independently manage the required digest pin.

The next block added `ip_governance/register_source.py` and `tests/ip_governance/test_register_source.py`. The loader rejects path traversal and symlink components, bounds reads, rejects duplicate JSON keys, applies the repository structural validator, computes raw and canonical digests, detects ordinary concurrent file replacement, and supports pinned raw digest verification. `ProtectedIPReleaseGate.from_authoritative_register(...)` reloads the register on every evaluation and binds the observed raw digest and source-relative path into the canonical provenance event. This is controlled-source reference code, not proof of source authenticity; production still needs a protected root and independently managed digest pin. The payload-injection constructor remains for test/reference use. The authoritative factory now requires an independently supplied raw SHA-256 pin and refuses to derive trust from the same file it loads.

The next design reassessment found a critical binding gap: prior eligibility could be requested for an asset without carrying the exact artifact digest/version, and authorization scope did not bind the destination. IP-07 now requires the requested artifact digest and version reference to match the canonical register, repeats those fields plus destination in the authorization record, and derives a deterministic authorization scope from canonical JSON of asset ID, artifact digest, version reference and destination. The canonical provenance event now records artifact digest as `content_hash`, version reference as `version_id`, rights snapshot digest separately, and includes destination/artifact/version in its deterministic event identity.

## Verified from GitHub

- `tests/ip_governance/test_release_gate.py`: 21 test methods authored.
- `tests/ip_governance/test_register_source.py`: 10 test methods authored.
- PR #75 remains open, draft, and unmerged.
- GitHub returned no workflow runs and no status checks for that latest commit. This is not a test pass.
- Thirty-one regression test methods across IP-07 gate/source-loader suites are authored but not executed against a canonical checkout.

## Latest design hardening — review validity and packaging

- Added the optional `review_validity_validator` adapter to IP-07. It receives the request, selected rights records, and current durable review events. If absent, returns anything other than literal `True`, or raises, the gate returns HOLD. This is a fail-closed integration contract, not an implementation of a freshness window: the authoritative service must apply a versioned policy and current revocation/disclosure state. No arbitrary review-age period was invented.
- Added three tests: missing validator, explicit denial, and validator exception all HOLD. Gate test count is now 21; source-loader tests remain 10.
- Updated `pyproject.toml` package discovery to include `ip_governance*`, addressing a packaging omission. Distribution build/install verification is still pending.
- Latest IP-07 commit at time of this update: `7f444930ec373834c057f1dca7a7af35c061407f`.

## Remaining gates

| Gate | State |
|---|---|
| Canonical ProvenanceEvent field-shape alignment | DONE in reference code |
| Canonical event-hash validation | DONE in reference code |
| Resolve asset against register and run repository structural validator | DONE in reference code |
| Controlled register loader, mandatory independent digest pin and provenance source binding | DONE in reference code; protected deployment root/pin custody pending |
| Standards-compliant JSON Schema validation | PENDING |
| Review freshness/revocation validator seam | DONE; authoritative policy/service integration pending |
| setuptools discovery includes `ip_governance*` | DONE in source config; distribution build/install verification pending |
| Enforce existing classification policy and trusted-destination adapter | DONE in reference code; final classification mapping review pending |
| Authoritative AuthorizationObject + revocation adapter | PENDING |
| Durable canonical provenance publisher + idempotency | PENDING |
| Independent checkpoint store/key custody | PENDING |
| Exact-revision test run and retained evidence | PENDING |
| Human-governed end-to-end release path | PENDING |
| Merge/release | HOLD |

Regression coverage includes malformed register dates, uppercase artifact digests, register/artifact mismatch, authorization digest/version/destination mismatch, source pin mismatch, path traversal, duplicate JSON keys, invalid JSON, size limits, symlinks, and in-memory snapshot mutation. Tests remain authored but unexecuted. No paid runner or workflow was triggered. No test-pass, legal-clearance, or production-readiness claim is made.

## Latest hardening — deterministic provenance idempotency and ambiguous-write recovery

**Private implementation PR:** https://github.com/phillipbarnard07-cell/DingoOS/pull/75  
**Implementation commit:** `ff66643b9c01bf8ec2b09b5de1b186d38330abec`  
**Regression-test commit:** `95b67570b001dbc2080b20222c4407e92d0be94f`  
**Specification commit:** `1bb32f7538c568700994db76aff6bec4d7feafa1`

- The canonical deterministic `event_id` is now the publisher's required idempotency key/receipt; arbitrary non-empty IDs are rejected.
- Added an optional fail-closed reconciliation adapter that receives the expected `event_id` and canonical `provenance_hash`. It may recover an ambiguous timeout only by affirming the exact durable event/hash; denial, exception, or no adapter leaves the decision at HOLD.
- The persisted event records `ELIGIBILITY_CANDIDATE` and `TECHNICAL_ELIGIBILITY_ONLY`, rather than claiming a final eligible decision before persistence has been confirmed. No authorization or release is implied.
- Added four gate regression methods: mismatched receipt, timeout reconciled against exact event/hash, timeout without confirmation, and reconciliation-service exception. Gate suite now has 25 authored methods; the source-loader suite has 10.
- Tests remain authored, not executed against a canonical checkout. No paid workflow/runner was triggered. Durable publisher integration, atomicity, conflict handling, and real store crash-recovery remain pending. Merge/release remains HOLD.

## Latest block — SQLite durable provenance reference adapter

**Current IP-07 implementation branch:** `feat/ip-07-provenance-release-gate`  
**Latest specification commit:** `749829882e30bb492d8dba8559756de835538c6c`

- Added `ip_governance/sqlite_provenance_store.py`: canonical event/hash validation, atomic SQLite append transaction, deterministic event-ID receipt, idempotent identical retries, conflict on same ID/different payload, append-only UPDATE/DELETE triggers, and exact payload/hash reconciliation.
- Gate integration now independently reconciles the exact event ID and hash after successful publish whenever the reconciler is configured. A fake receipt without a durable write remains HOLD.
- Added `tests/ip_governance/test_sqlite_provenance_store.py` with 9 authored test methods; added 3 integration methods to the gate suite. Current authored test-method counts: gate 28, SQLite provenance store 9, register-source 10.
- Store uses only Python standard library; no paid service, hosted runner, or network dependency introduced.
- Verification remains pending: tests were not executed in this environment. Local SQLite behavior is not evidence of production crash durability, external replication, independent custody, backup/restore, or concurrency safety under target deployment.
- Authoritative AuthorizationObject/revocation integration, protected database/filesystem access, independent checkpoint trust, package build/install, end-to-end human authorization, and exact-revision test evidence remain pending. Merge/release remains HOLD.

### Follow-up consistency hardening

The provenance-store source was rechecked against the current PR head and corrected to reuse IP-07's canonical ProvenanceEvent validator and explicitly close read/initialization connections. Current implementation commit recorded in the prior update: `cedda262c9f4de23ea3f3e3d1eb44f851d6496ab`. This is a source-level consistency update only; tests still have not been executed. Merge/release remains HOLD.

## Production-readiness closure criteria — 2026-10-10

**Acceptance contract:** https://github.com/phillipbarnard07-cell/DingoOS/blob/feat/ip-07-provenance-release-gate/docs/ip/IP-PRODUCTION-READINESS-EXIT-CRITERIA.md  
**Acceptance-contract commit:** `6a4410d722682661e1dbc717c0193f4af0829700`

The objective is now explicitly production-ready DingoOS IP, not indefinite hardening. The acceptance contract separates technical production readiness from legal/commercial IP readiness and sets finite gates for canonical scope, reproducible build, executed exact-revision tests, rights/asset status, authorization separation, durable provenance, security/confidentiality, operations/recovery, and independent evidence review.

**Current decision remains NOT ESTABLISHED / NO-GO / HOLD.** The new document defines acceptance criteria only; it does not pass any gate. The next work should close mandatory blockers and produce executed evidence, not add optional architecture for its own sake. No paid workflow was triggered.


## IP-07 next block — SQLite concurrency and rollback regression coverage

**Source test commit:** `6b6e2e849c29a2a1dc6128df4c1f4393bea81c2f`  
**Specification commit:** `5185e9ca06550fa81c8b47c074fa3c0a9e34072a`  
**Branch:** `feat/ip-07-provenance-release-gate`

Added three standard-library regression methods to `tests/ip_governance/test_sqlite_provenance_store.py`:

- Concurrent identical publishes from eight independent store instances must be idempotent and leave one exact event.
- Concurrent valid but conflicting payloads reusing one event ID must yield one stored winner and one explicit conflict; the winner must remain intact.
- A manually interrupted/uncommitted transaction rolled back before commit must not become visible through store reads or reconciliation.

The SQLite store suite now has **12 authored test methods** (previously 9); the IP-07 gate suite remains 28 and register-source suite remains 10. Updated `docs/ip/IP-07-PROVENANCE-BOUND-RELEASE-ELIGIBILITY-GATE.md` records these cases, exact local test commands, and the distinction between authored tests and executed evidence.

**Verification status:** tests were not executed in this environment. No CI workflow was triggered. These tests improve the intended regression coverage but do not establish target-environment concurrency, crash recovery, backup/restore, independent custody, or production readiness. PR #75 remains open/draft/unmerged; merge and release remain HOLD.


## Cross-stack production design reassessment — 2026-10-10

**Assessment report:** https://github.com/phillipbarnard07-cell/DingoOS/blob/feat/ip-07-provenance-release-gate/docs/ip/IP-PRODUCTION-DESIGN-REASSESSMENT-2026-10-10.md  
**Current IP-07 PR head after this block:** `0d6aa7864c1d8f5a5721354654ad765b283a36a6`  
**Status:** Source/design assessment; not test execution, legal clearance, or production certification.

Reviewed the live IP-03 → IP-07 stack and canonical IP register, provenance and release-manifest schemas, policy primitives, package configuration, and production acceptance contract. PRs #71–#75 remain a stacked chain of open draft PRs; each layer must be reviewed and tested against the exact dependency revisions, then integrated and verified in order.

### Findings preserved in the reassessment

- IP-03's scoped rights model and fail-closed required-domain checks are useful, but unresolved asset states remain blockers rather than ownership claims.
- IP-04's `ReviewHistory` is an in-memory reference implementation; it is not durable production history.
- IP-05's SQLite ledger has authorization-validator integration and local append-only triggers, but local SQLite does not establish independent custody or tamper-proof storage.
- IP-06's HMAC checkpoint protocol is a useful contract; the in-memory/local file stores do not meet independent production trust custody.
- IP-07's maximum positive state remains eligibility for human authorization, not authorization or release. Authoritative authorization/revocation, review freshness, protected source-pin custody, classification mapping, schema validation, operational recovery, and exact-revision execution remain blockers.
- Canonical Draft 2020-12 schemas exist; the current runtime validators are structural, not full standards-compliant JSON Schema validators.
- Register classifications and legacy C0–C4 policy vocabulary require an explicit, tested conservative mapping.

### Concrete defect fixed

Source review found that `SQLiteReviewLedger` used the SQLite connection context manager in its initialization/read paths, which manages transactions but does not itself close the connection. The code now explicitly closes these connections with `contextlib.closing`. Added a regression test that tracks and asserts closure for both paths.

- Source commit: `19b8abac5b043158f7b1f371aefd45509d21dcb4`
- Test commit: `7902ec178ba075df16cd0e10050bdb7298cdb8f1`
- Reassessment report commit: `0d6aa7864c1d8f5a5721354654ad765b283a36a6`
- The review-ledger suite now has 8 authored test methods. The new test and suite have not been executed in this environment.

### Decision and next block

**Production readiness: NOT ESTABLISHED. Merge/release: HOLD.** No paid runner/workflow was triggered. The finite next steps are execute the focused regression and complete local suites, fix observed failures, integrate authoritative authorization/revocation and conservative classification policy, establish independent checkpoint/backup/recovery controls, verify schema/package/build behavior, then produce revision-bound evidence and asset-specific rights decisions. Do not expand architecture unless verification demonstrates a material gap.


### Upstream architecture dependency warning

A follow-up dependency check found that IP-03 PR #71 is based on `d49728297cd14c32f8945b7a1040a8951b21f494`, the observed head of open/unmerged PR #69 (repository architecture integration). Related cross-cutting PR #64 (evolutionary GitHub substrate) and #68 (canonical system manifest/Snowflake continuity) are also open/unmerged, with #68 based on a separate alpha/beta/gamma/sigma integration branch.

This means the IP stack's upstream system baseline is not yet proven to be the final accepted architecture. The reassessment report now marks dependency reconciliation against the canonical system manifest and relevant architecture/security/governance PRs as a release-critical integration gate. Do not merge unrelated PRs automatically; compare diffs and dependency relationships first.

Latest reassessment report commit: `f54d678baf0655365a0bf8eefba859399e73c1fe`. Current PR #75 remains draft/unmerged. No tests were executed and no paid workflow was triggered. Production readiness remains NOT ESTABLISHED; merge/release HOLD.


## Next-block implementation — IP-03 malformed-input hardening and asset isolation

Source review found that runtime values are not enforced by Python dataclass annotations. Invalid values could raise exceptions during rights validation; additionally, the reusable rights blocker function did not enforce that all supplied records belonged to one asset.

The rights ledger now validates runtime field types, rejects malformed evidence and non-record inputs, rejects invalid/duplicate required domains, and blocks mixed-asset evaluations. Five regression methods were added covering malformed record/evidence fields, invalid state, non-record/invalid-domain input, and mixed asset IDs.

Changes are committed to both the IP-03 source branch and the IP-07 candidate branch:
- IP-03 source: `096f13ef624c00783aeae5c3b7ed5da21fb54b88`
- IP-03 tests / updated PR head: `acf17eedc5aedbc1b65f97be903473cd73117d76`
- IP-07 candidate source: `ab7d25b1dc448c9758f49f287fb06757553dad65`, `6a1fde26ea9dcc9c91e0346d68cde1ee74327d86`
- IP-07 candidate tests: `7a68cee8ba048a1757fb7d3dc677bb2d85bfdd68`
- Reassessment report update: `b5e4e4377486e5a6a8a54747ddcca5a935830f98`

IP-03 rights-ledger suite now has **11 authored test methods** (previously 6). Tests have not been executed; authored coverage is not a pass. PR #71 remains draft/unmerged, and PR #75 remains draft/unmerged. Production status remains NOT ESTABLISHED / HOLD. No paid workflow was triggered.


## Next block — versioned conservative classification mapping

The register-to-legacy IP-class mapping is now formalized as policy `DGO-IP-CLASS-MAP-1.0.0` and implemented in `security/ip_classification_mapping.py`. The IP-07 gate uses this shared mapping instead of a private inline table and records the policy version in canonical provenance parameters and deterministic event identity.

Mapping: PUBLIC→C0, INTERNAL→C1, CONFIDENTIAL→C2, RESTRICTED→C3, PRIVATE_IP→C4, SECRET→C4. Unknown, missing, malformed or case-variant labels do not map and block the gate. This mapping is conservative engineering policy, not legal classification or authorization; it still requires authorized policy-owner review before production.

Policy: https://github.com/phillipbarnard07-cell/DingoOS/blob/feat/ip-07-provenance-release-gate/docs/ip/IP-CLASSIFICATION-MAPPING-1.0.0.md

Commits:
- Mapping module `5bdd110b08c1d59c092ebeff7c5ff77e44ff7d46`
- Mapping tests `aeb0f0deee50f0cb7a40f397ba7c6c6b116c9248`
- Gate integration `c645fb2576328b712affa27df9ed4bd5c771d4c8`
- Provenance policy-version binding `82e504d6fc6b919e2db14f3486127bf6f84d5dc5`
- Gate tests `8d2b381f56c4a11e3fb0d86dd301a63d3e145d0a`
- Policy document `fb5fba814c7f25ca6b2e83aac4ab476a541290c5`
- Reassessment report update: `df69e4e3c972bff30200c2599815326d7d112070`

The IP-07 release-gate suite now has 30 authored methods. Mapping and integration tests remain unexecuted; no PASS is claimed. PR #75 remains draft/unmerged. Production status remains NOT VERIFIED / NO-GO / HOLD. No paid workflow was triggered.


## Next block — free, revision-bound verification harness

Added a local verification runner and protocol because the available GitHub content operations cannot execute the candidate's code, and authored tests must not be reported as passing.

- Runner: https://github.com/phillipbarnard07-cell/DingoOS/blob/feat/ip-07-provenance-release-gate/scripts/verify_ip_candidate.py
- Protocol: https://github.com/phillipbarnard07-cell/DingoOS/blob/feat/ip-07-provenance-release-gate/docs/ip/IP-CANDIDATE-LOCAL-VERIFICATION-PROTOCOL-1.0.0.md
- Reassessment update: `255c53abb4582379196b72f636d1fb0d3f4aca89`

The runner refuses to execute unless the supplied full candidate SHA matches the checkout and the working tree is clean. It runs eight test files independently via `unittest discover`, records commands, UTC times, exit codes, runtime details and SHA-256 hashes of captured stdout/stderr, then attempts a local wheel build with `--no-deps --no-build-isolation`. It does not install dependencies, invoke paid CI, or grant authorization. Local output is written under ignored `artifacts/ip-verification/`; logs require review before sharing.

Commits:
- Runner initial: `f52a12bb2b42725ff1164215ca9e1b12c9250435`
- Runner hardening: `e7deb0cdfd8cae82e676d833e4098d0e64624ebe`
- Local evidence ignore rule: `8d316379114a1b5767090140f525cbd434a831ac`
- Protocol: `9d88e26744096f7e5b601279b666071a37566965`
- Protocol clarification: `fc36667aadaeab9bd1fb9d0fc4435f7bb55d6738`

**The runner has not been executed here.** This is tooling and procedure, not test evidence. Test execution, clean wheel-build evidence, authorized classification-policy approval, upstream PR reconciliation, independent durability and rights clearance remain open. PR #75 remains draft/unmerged. Production remains NOT VERIFIED / NO-GO / HOLD. No paid workflow was triggered.
