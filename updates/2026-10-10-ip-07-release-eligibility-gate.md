# Engineering update — 2026-10-10: IP-07 pending-work review

**Workstream:** DingoOS protected-IP governance and provenance  
**Private implementation PR:** https://github.com/phillipbarnard07-cell/DingoOS/pull/75  
**Latest source revision:** `8263b182d0330b57444010fdfee9077e55456e7a`  
**State:** Canonical provenance event shape implemented; runtime adapters and execution evidence pending

## New findings and corrections

The IP-07 pending review inspected the actual schemas on the IP-06 dependency branch:

- `schemas/provenance/provenance-event.schema.json`
- `schemas/ip-asset-register-v1.schema.json`
- `schemas/ip-release-manifest-v1.schema.json`
- `security/ip_policy.py`
- `security/integrity.py`

IP-07 has been corrected from its earlier custom envelope to the canonical ProvenanceEvent field shape (`event_id`, `object_id`, `version_id`, `content_hash`, `provenance_hash`, `event_type`, plus only permitted optional fields). A standard-library validator checks required/allowed fields, types, digest formats, and the canonical event hash. The test asset ID was changed to match the canonical `DGO-IP-...` format.

The canonical release-manifest schema requires `authorization_status: NOT_GRANTED`. It must not be treated as a grant or altered to imply release approval. The canonical asset register is now a required input. The gate calls the repository-maintained `scripts.validate_ip_register.validate_register` structural validator on each evaluation, checks the requested asset's release-relevant review states, requires an exact lowercase artifact digest, and applies the existing trusted-destination/classification policy. This structural validator is not a standards-complete JSON Schema engine, and authoritative file loading remains pending.

The next design reassessment found a critical binding gap: prior eligibility could be requested for an asset without carrying the exact artifact digest/version, and authorization scope did not bind the destination. IP-07 now requires the requested artifact digest and version reference to match the canonical register, repeats those fields plus destination in the authorization record, and derives a deterministic authorization scope from canonical JSON of asset ID, artifact digest, version reference and destination. The canonical provenance event now records artifact digest as `content_hash`, version reference as `version_id`, rights snapshot digest separately, and includes destination/artifact/version in its deterministic event identity.

## Verified from GitHub

- Latest IP-07 documentation commit at time of this update: `25ef5293f2fc8b81aedb5271976de6583246daa9`.
- PR #75 remains open, draft, and unmerged.
- GitHub returned no workflow runs and no status checks for that latest commit. This is not a test pass.
- Seventeen regression test methods are authored but not executed against a canonical checkout.

## Remaining gates

| Gate | State |
|---|---|
| Canonical ProvenanceEvent field-shape alignment | DONE in reference code |
| Canonical event-hash validation | DONE in reference code |
| Resolve asset against injected register and run repository structural validator | DONE in reference code; authoritative file loading and standards-compliant JSON Schema validation pending |
| Enforce existing classification policy and trusted-destination adapter | DONE in reference code; final mapping/freshness/revocation review pending |
| Authoritative AuthorizationObject + revocation adapter | PENDING |
| Durable canonical provenance publisher + idempotency | PENDING |
| Independent checkpoint store/key custody | PENDING |
| Exact-revision test run and retained evidence | PENDING |
| Human-governed end-to-end release path | PENDING |
| Merge/release | HOLD |

New regression cases cover malformed register dates, uppercase artifact digests, register/artifact mismatch, and authorization digest/version/destination mismatch. No paid runner or workflow was triggered. No test-pass, legal-clearance, or production-readiness claim is made.
