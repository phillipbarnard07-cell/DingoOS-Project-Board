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
