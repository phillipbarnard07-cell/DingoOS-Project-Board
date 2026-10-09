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


## Git revision binding block

Added `--bind-git-revision` to the offline validator. Used with `--checkout-root .`, it requires the current checkout to be clean and non-detached, binds report pre/post revisions to `git rev-parse HEAD`, binds report branches to the current branch, and verifies each inventoried file against the committed Git blob at that revision. Untracked/generated inventory entries are rejected as not present in the declared commit.

Added regression cases for a matching clean commit, revision mismatch plus dirty tree, and inventory entries absent from the commit. Updated the formal contract with the command and explicit limits.

Command:

```bash
python scripts/verify_foundation_evidence.py artifacts/verification/foundation-ci.json --checkout-root . --bind-git-revision
```

Files on `integration/all-github-repositories`: validator, validator tests, and validation contract. Execution remains **NOT VERIFIED**; these tests have not been run here. No paid CI, merge, release, scientific validation, or deployment authorization is claimed.


## Reassessment correction — independent expected revision

Reassessment found that matching a report to local `HEAD` alone still lets the report/checkout pair agree on an unintended revision. Revision binding now requires `--expected-revision`, an independently supplied full lowercase 40-character SHA. The checkout `HEAD` and both report revision fields must match that value. Added a regression test for an expected-SHA mismatch and updated the formal contract.

Use the SHA copied independently from the reviewed commit:

```bash
python scripts/verify_foundation_evidence.py artifacts/verification/foundation-ci.json --checkout-root . --bind-git-revision --expected-revision <full-40-character-commit-sha>
```

Source commits pushed; execution remains **NOT VERIFIED**. This is a revision identity check, not producer authentication, inventory completeness, or proof that test commands ran.


## Reassessment follow-through — trusted revision and inventory completeness

Further review found two gaps: local `HEAD` agreement alone did not select a revision independently, and verifying only listed hashes could not detect omitted tracked source. Corrected as follows:

- `--bind-git-revision` now requires `--expected-revision`, a full lowercase SHA supplied independently by the reviewer; checkout `HEAD` and both report revision fields must match.
- The runner now inventories Git-tracked files rather than every filesystem file, avoiding ignored local environments and generated files being mistaken for source.
- Revision binding compares the declared inventory path set against tracked source paths and rejects omissions and unexpected entries.
- Added regression source for an expected-revision mismatch and an omitted tracked source file.

Use:

```bash
python scripts/verify_foundation_evidence.py artifacts/verification/foundation-ci.json --checkout-root . --bind-git-revision --expected-revision <full-40-character-commit-sha>
```

Files changed on `integration/all-github-repositories`: runner, validator, validator tests and contract. **Tests have not been executed here.** This is source-level hardening only; do not infer a passing runner, merge readiness, scientific validation, or deployment authorization.


## 2026-10-09 reassessment — cross-platform path validation

A further source audit found that the artifact path guard rejected doubled backslashes but could admit a single backslash. This is unsafe for cross-platform path interpretation because Windows treats backslashes as separators.

Corrective commits on integration/all-github-repositories now:
- reject any backslash in the structural inventory validator;
- apply the same check in checkout-backed inventory verification and Git revision binding;
- expand regression inputs to single-backslash paths, backslash traversal, and doubled backslashes;
- add a direct checkout-inventory regression case;
- document the cross-platform path contract.

Commits:
- efd40bef77218adf4544eddd8265d2e1eee881cb — initial structural guard
- 977e085b9a4f2fdcf51234e59f0f35bc07f5c4ff — defense-in-depth checks
- cabc4e431f02abea0abcd212be4fc8e6b8150d01 — regression cases
- 4e060674e18c08ecc6d2ecdbd89634fc9d555c13 — direct helper regression
- a34036c180edde82dc52d033245799b3ebf07aa3 — contract update

Evidence status: source changes are committed; regression tests remain NOT EXECUTED. PR #69 remains open/unmerged; release readiness remains HOLD. GitHub Actions query for the prior head returned zero runs, and no paid CI requirement was introduced. The next gate remains real test execution on the exact reviewed revision.


## 2026-10-09 reassessment — Git NUL delimiter correction

A further review found that both Git tracked-path readers used the wrong byte delimiter for git ls-files -z. Git's -z mode separates filenames with actual NUL bytes. The runner inventory and revision-binding verifier have now been corrected to split on the NUL byte. Added a runner regression fixture with two committed files (including a space in one filename) to assert that the inventory emits two distinct paths. Existing revision-binding regression cases exercise tracked-source completeness against a real temporary Git repository.

Commits:
- 56de448e9b51898532ed01a0a20aaed273b6c67a — runner NUL delimiter
- fb7c929eb7fafbaa0956a42320b1700108d5bfbe — verifier NUL delimiter
- 9cd4c7b1248f41751bc6e849ec16b877114a008f — tracked-path regression fixture
- c2cc8740b05cde5a4a92e65d9019e673d72d6b3d — contract update
- f2ff635f7639ee70dffca46d37759cdc063dbbed — clarify the NUL byte literal in the contract

Tests remain NOT EXECUTED. PR #69 remains open/unmerged, and no Actions runs were returned for the prior reviewed head. Do not promote the merge gate until the targeted regression suite is executed against the current exact revision. No paid CI dependency added.
