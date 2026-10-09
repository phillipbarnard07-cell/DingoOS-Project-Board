# DingoOS IP-02 — immutable artifact proof and manifest

**Date:** 2026-10-10  
**Main repository:** phillipbarnard07-cell/DingoOS  
**Branch:** integration/all-github-repositories  
**Status:** Implementation committed; 14 isolated regression tests passed; canonical checkout verification pending.

## Delivered
- scripts/ip_release_manifest.py: deterministic manifest builder/verifier using Python standard library and Git only.
- schemas/ip-release-manifest-v1.schema.json: strict manifest contract with authorization fixed to NOT_GRANTED.
- ip/release-scope.v1.json: explicit internal integrity archive scope with 13 selected files.
- tests/test_ip_release_manifest.py: 12 tests covering deterministic construction, matching integrity, content tampering, source revision mismatch, traversal, unknown fields, duplicate paths, symlinks, forbidden authorization promotion, size mismatch, malformed state types, and scope/register consistency.
- docs/ip/DINGOOS-IMMUTABLE-ARTIFACT-MANIFEST-V1.md: runbook, findings, limitations and unresolved-evidence register.
- ip/register.v1.json expanded to 13 asset records, retaining conservative ownership/contribution/third-party/disclosure states.

## Verification truth
Fourteen tests passed in an isolated local harness for the implementation logic. This is not a run of the canonical repository test suite. Actual manifest generation and verification on a clean canonical checkout remain pending.

## Important boundaries
Manifest SHA-256 binds bytes to a digest; it does not prove authorship, legal ownership, trusted time, patentability, confidentiality, third-party clearance or scientific truth. A detached digest sidecar is not a signature and requires an independently trusted anchor for stronger proof. The tool requires each scope asset to exist in the IP register and requires classification/ownership/third-party/disclosure states to match exactly. It recomputes the expected manifest during verification. The tool always returns authorization_status NOT_GRANTED and does not authorize external disclosure.

Ownership/chain of title, contributor assignments, third-party rights, patentability/prior-art review, trade-secret status and trademark rights remain unresolved because the project records do not contain the required primary evidence. External legal-source research was not available through connected tools in this execution; this is not an Australian legal opinion.

## Clean-checkout commands
    python -m unittest discover -s tests -p 'test_ip_release_manifest.py' -v
    python scripts/ip_release_manifest.py create
    python scripts/ip_release_manifest.py verify

No paid service, subscription, billing gate or external runtime dependency was added.
