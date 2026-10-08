# Foundation evidence integrity: post-run stability gate — 2026-10-09

**Canonical branch:** `integration/all-github-repositories`  
**Integration PR:** https://github.com/phillipbarnard07-cell/DingoOS/pull/69  
**Status:** implementation and regression source committed; execution NOT VERIFIED.

## Reassessment finding

The previous runner captured repository revision and working-tree state before compilation, deterministic gates and regression tests. A pre-run snapshot alone cannot show whether the checkout changed during those commands. It also did not re-check the origin after gate execution.

## Implemented

The runner now records both pre-run and post-run Git revision, working-tree cleanliness and normalized canonical repository origin. The report returns PASS only if the pre/post revision and origin match, both working-tree snapshots are clean, and every required command passes. Otherwise the result is HOLD. The report schema remains v1 and gains additive post-run fields.

## Formal invariant

Let r0/r1 be source revisions, o0/o1 normalized origins, and w0/w1 pre/post clean flags.

`StableCheckout = (r0 == r1) AND (o0 == o1) AND w0 AND w1`

PASS additionally requires all required gate commands to return zero. The report digest is a checksum, not a cryptographic signature or proof that external CI ran.

## Files

- `scripts/run_foundation_ci.py`
- `tests/core/test_foundation_ci_runner.py`
- `docs/architecture/FOUNDATION-RUNNER-PROVENANCE-CONTRACT.md`

Regression tests cover mutation of working-tree state, revision and origin across the gate sequence.

## Verification boundary and next action

The GitHub file API was used to commit the code; no test execution is claimed. On a usable canonical checkout, run:

```bash
python -m pytest -q tests/core/test_foundation_ci_runner.py
python -m pytest -q tests/core/test_csre3_controlled_test_contract.py tests/core/test_csre4_independent_validation_contract.py
python scripts/run_foundation_ci.py
```

Inspect `artifacts/verification/foundation-ci.json` and compare its source revision with the reviewed branch head. Do not mark tests or CI PASS until actual output has been inspected. No paid CI requirement, merge, release, scientific validation or deployment authorization is introduced.
