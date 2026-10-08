# CSRE verification command correction — 2026-10-09

This note supersedes the command-path suggestion in the earlier CSRE reassessment note.

The pytest-collected CSRE3 and CSRE4 contract tests are:

- `tests/core/test_csre3_controlled_test_contract.py`
- `tests/core/test_csre4_independent_validation_contract.py`

On a usable, clean named-branch checkout, run:

```bash
python -m compileall -q core scripts
python -m pytest -q tests/core/test_csre3_controlled_test_contract.py tests/core/test_csre4_independent_validation_contract.py
python scripts/run_foundation_ci.py
```

The final command is the canonical foundation verification and will run the configured foundation regression targets, record branch/revision/working-tree provenance, write `artifacts/verification/foundation-ci.json`, and return nonzero on HOLD.

**Execution status remains NOT VERIFIED.** This ChatGPT environment could not resolve `github.com` to create a local checkout. The commands above are not claimed to have run. Do not mark the board's test or CI status PASS until the artifact from an actual run has been inspected and tied to the exact source revision.

The project remains zero-cost; do not enable paid hosted CI or treat a workflow with no executable steps as source-code test evidence.
