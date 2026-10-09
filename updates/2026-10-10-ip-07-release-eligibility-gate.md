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
