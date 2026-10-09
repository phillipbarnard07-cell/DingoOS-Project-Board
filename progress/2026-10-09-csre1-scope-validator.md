# DingoOS progress — CSRE-1 SCOPE contract implementation (2026-10-09)

**Parent design:** [CSRE-1 resonance protocol v1.0](https://github.com/phillipbarnard07-cell/DingoOS/blob/integration/all-github-repositories/docs/research/CSRE-1_RESONANCE_POSTULATE_AND_TEST_PROTOCOL_V1.md)  
**Implementation guide:** [SCOPE validator contract](https://github.com/phillipbarnard07-cell/DingoOS/blob/integration/all-github-repositories/docs/research/CSRE1_SCOPE_VALIDATOR_V1.md)  
**State:** SOURCE IMPLEMENTED; EXECUTION NOT VERIFIED; research release remains HOLD.

## Files committed

- [JSON Schema](https://github.com/phillipbarnard07-cell/DingoOS/blob/integration/all-github-repositories/schemas/research/csre1-scope-v1.schema.json) — manifest structure and core constraints.
- [Python validator](https://github.com/phillipbarnard07-cell/DingoOS/blob/integration/all-github-repositories/dingoos/research/csre1_scope.py) — stdlib-only canonical digest, fail-closed cross-field checks, optional calibration registry check, frozen-digest comparison and bounded outcome mapping.
- [Regression tests](https://github.com/phillipbarnard07-cell/DingoOS/blob/integration/all-github-repositories/tests/research/test_csre1_scope.py) — valid manifest and negative cases for missing endpoint, missing unit, post-preregistration edit, unknown calibration, missing null control, prohibited SEARM bypass, NaN, invalid run mapping and non-boolean flags.
- [Package markers](https://github.com/phillipbarnard07-cell/DingoOS/tree/integration/all-github-repositories/tests/research) — make focused tests importable.
- [Implementation guide](https://github.com/phillipbarnard07-cell/DingoOS/blob/integration/all-github-repositories/docs/research/CSRE1_SCOPE_VALIDATOR_V1.md) — contract, invocation and limitations.

## Reassessment and safeguards

The validator uses Python's standard library only. It does not require hosted CI, paid services, or external packages. The manifest digest binds all fields except its own digest field; the expected digest should also be retained independently at preregistration time. Calibration existence is checked only when a trusted calibration registry is passed. A digest is integrity evidence, not a trusted timestamp or proof of scientific correctness.

The outcome mapper emits only SUPPORTS_PREREGISTERED_MODEL, MODEL_REFUTED, INCONCLUSIVE or INVALID_RUN. It does not promote epistemic state; SEARM remains authoritative.

## Verification boundary

The source and test files are committed, but tests have **not** been executed in a repository checkout during this turn. No passing test count is claimed. Run from repository root:

```sh
python -m unittest tests.research.test_csre1_scope -v
```

Any failures must be fixed and re-run before describing the slice as tested or verified. No scientific result, apparatus validation or deployment readiness is claimed.

## Next block

Run the focused tests in an available local checkout without enabling paid CI. Fix and rerun failures; then integrate with the existing ResearchObject / EvidenceObject / ProvenanceEvent / C-4PO / SEARM contracts through adapters and add provenance events for the frozen preregistration digest.


## Reassessment follow-up — static hardening (2026-10-09)

A second review of the committed source found and corrected a latent frozen-digest comparison call that used the wrong module. The validator now uses constant-time `hmac.compare_digest` for both the embedded manifest digest and independently stored preregistration digest.

The validator was tightened to reject unknown nested keys, matching the JSON Schema's `additionalProperties: false` intent for endpoint, frequency band, effect threshold, model comparison and controls. A malformed non-string expected digest now fails closed instead of raising a regular-expression type error.

Regression coverage was expanded to **14 test methods**, including independently frozen digest acceptance/tamper detection, unknown nested fields and malformed expected digest types. The JSON Schema was fetched and parsed successfully; committed source and test files were re-fetched from GitHub and their expected contents confirmed.

**Execution boundary unchanged:** the test suite has not run. The available local shell cannot resolve `github.com`, and this session does not have a checked-out repository working tree. Do not infer test success from static inspection. Next step remains running `python -m unittest tests.research.test_csre1_scope -v` in a local checkout with no paid CI.
