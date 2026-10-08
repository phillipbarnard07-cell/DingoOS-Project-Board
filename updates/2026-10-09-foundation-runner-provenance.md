# Foundation runner provenance hardening — 2026-10-09

**Canonical branch:** `integration/all-github-repositories`  
**Integration PR:** https://github.com/phillipbarnard07-cell/DingoOS/pull/69  
**Status:** source and regression tests committed; execution NOT VERIFIED.

## Change

The local no-paid-CI foundation runner previously recorded a hard-coded repository label without checking the checkout's configured Git origin. That could let a report generated from a different repository identify itself as DingoOS evidence.

The runner now resolves `origin`, normalizes its owner/repository slug, and fails closed unless it matches `phillipbarnard07-cell/DingoOS`. Only HTTPS, SSH URL form and Git's scp-style `git@github.com:...` form are accepted. Insecure or unsupported schemes, other hosts and noncanonical repositories are rejected. The report records the normalized slug only; it does not store or print the full remote URL, which might contain credentials.

## Files

- `scripts/run_foundation_ci.py`
- `tests/core/test_foundation_ci_runner.py`
- `docs/architecture/FOUNDATION-RUNNER-PROVENANCE-CONTRACT.md`

Regression source includes URL normalization, malformed/non-GitHub/insecure remote rejection, noncanonical repository rejection, dirty-tree HOLD and detached-HEAD rejection.

## Verification boundary

This update was made through GitHub's file API; the tests were not executed here. Do not mark foundation verification PASS until a usable local checkout runs:

```bash
python -m pytest -q tests/core/test_foundation_ci_runner.py
python scripts/run_foundation_ci.py
```

Review `artifacts/verification/foundation-ci.json` and confirm it identifies the exact source revision. The broader CSRE3/CSRE4 contract tests must also be run. GitHub Actions remains non-evidence unless its actual job steps and logs are observable. No paid CI dependency, merge, release, physical experiment, or scientific validation is claimed.
