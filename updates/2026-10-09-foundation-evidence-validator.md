# Offline foundation evidence report validator — 2026-10-09

**Canonical branch:** `integration/all-github-repositories`  
**Integration PR:** https://github.com/phillipbarnard07-cell/DingoOS/pull/69  
**Status:** source and adversarial tests committed; tests NOT EXECUTED.

## Reassessment finding

The runner emits a deterministic SHA-256 digest, but consumers had no independent tool to recompute it and check whether a report claiming PASS actually satisfies the runner's stated stability and gate conditions. A digest alone is not a signature and a report can be internally inconsistent.

## Implemented

- `scripts/verify_foundation_evidence.py`: offline JSON schema, digest and PASS/HOLD consistency validator. It also validates field types and shapes in revisions, tree entries, command records, artifact inventory and claims for both PASS and HOLD reports.
- `tests/core/test_foundation_evidence_validator.py`: adversarial tests for tampering, false PASS claims, failed gates, dirty trees, changed revision, schema/field errors, and scientific-validation overclaim.
- `docs/architecture/FOUNDATION-EVIDENCE-REPORT-VALIDATION-CONTRACT.md`: digest equation, PASS predicate, CLI exit semantics, limits and review requirements.

## Formal boundary

For canonical JSON encoding (C) and report (R), validation recomputes:

`SHA256(C(R without evidence_digest)) == evidence_digest`

PASS is accepted only when pre/post revision, branch and origin match; both tree snapshots are clean; every gate has `passed=true` and return code zero; and the report explicitly does not claim scientific validation or consequential deployment authorization.

The validator's exit code 0 means the report is internally valid, **not** necessarily that its `FOUNDATION_RESULT` is PASS. A valid HOLD report may exit 0; reviewers must inspect both output fields.

## Files

- https://github.com/phillipbarnard07-cell/DingoOS/blob/integration/all-github-repositories/scripts/verify_foundation_evidence.py
- https://github.com/phillipbarnard07-cell/DingoOS/blob/integration/all-github-repositories/tests/core/test_foundation_evidence_validator.py
- https://github.com/phillipbarnard07-cell/DingoOS/blob/integration/all-github-repositories/docs/architecture/FOUNDATION-EVIDENCE-REPORT-VALIDATION-CONTRACT.md

## Verification command

After the foundation runner emits a report:

```bash
python -m pytest -q tests/core/test_foundation_ci_runner.py tests/core/test_foundation_evidence_validator.py
python scripts/run_foundation_ci.py
python scripts/verify_foundation_evidence.py artifacts/verification/foundation-ci.json
```

No tests are claimed executed in this environment. The cross-component test is committed as source but remains unexecuted pending a usable checkout. No hosted/paid CI dependency, merge, release, scientific validation or deployment authorization is introduced. The next gate remains obtaining real local execution output and reviewing the exact source revision.


## Defensive-hardening follow-up

The next validator-hardening pass is committed on `integration/all-github-repositories`:

- Type-checks the report outcome before comparison so malformed JSON values produce validation errors rather than an uncaught exception.
- Requires the canonical runner and canonical DingoOS repository identity.
- Rejects POSIX absolute, Windows drive-qualified, traversal-based, backslash-separated, and duplicate artifact-inventory paths. A Windows drive-path regression case was added during final reassessment.
- Adds adversarial regression tests for each case.

Files: `scripts/verify_foundation_evidence.py`, `tests/core/test_foundation_evidence_validator.py`, and `docs/architecture/FOUNDATION-EVIDENCE-REPORT-VALIDATION-CONTRACT.md`.

Status remains **execution NOT VERIFIED**. The changes are pushed and re-fetched from GitHub, but test execution is not available in this workflow. No paid CI requirement, merge, release, scientific validation, or deployment authorization is introduced.


## Checkout-backed inventory verification block

Added optional local verification of artifact inventory entries against an actual checkout:

- `scripts/verify_foundation_evidence.py --checkout-root .` hashes each listed file and checks byte length.
- Missing files, byte-count or SHA-256 mismatches, symlink components, and paths outside the checkout are rejected.
- Without `--checkout-root`, the CLI labels the inventory as `STRUCTURE_ONLY`; with successful checkout verification it reports `VERIFIED_AGAINST_CHECKOUT`.
- Added regression cases for matching file bytes, modified file bytes, missing files, and symlink rejection.
- Formalized the verification boundary and example command in the architecture contract.

Files changed on `integration/all-github-repositories`: `scripts/verify_foundation_evidence.py`, `tests/core/test_foundation_evidence_validator.py`, and `docs/architecture/FOUNDATION-EVIDENCE-REPORT-VALIDATION-CONTRACT.md`.

**Execution remains NOT VERIFIED.** The source and tests are committed but have not been executed here. Inventory verification does not prove completeness, source revision identity, producer authenticity, or actual gate execution. No paid CI, merge, release, scientific validation, or deployment authorization is claimed.
