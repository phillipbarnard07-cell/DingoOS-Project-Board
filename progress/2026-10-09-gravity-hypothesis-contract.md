# Progress — Typed Gravity Hypothesis Contract

**Date:** 2026-10-09  
**Status:** Draft schema, baseline-control instance and review policy committed; remote content verification pending; runtime JSON Schema validation and transition tests not run.

## Added on `integration/all-github-repositories`
- `schemas/research/gravity-hypothesis-v1.schema.json`
- `schemas/research/gravity-hypothesis-baseline-control-v1.json`
- `docs/research/GRAVITY_HYPOTHESIS_STATE_AND_REVIEW_POLICY_V1.md`

## Contract coverage
The extension builds on the existing `schemas/research/hypothesis.schema.json` concept and adds:
- relationship to an established baseline model;
- recovery limits and formal equations/symbols/units;
- quantitative discriminating predictions and uncertainty;
- falsifiers, competing explanations and evaluation plans;
- separate SEARM qualification and C-4PO challenge records;
- append-only provenance and restricted IP disclosure controls.

## Important limitations
- JSON Schema validates record shape, not whether physics is true or whether a state transition was authorized.
- The policy requires new records to start SPECULATIVE_HYPOTHESIS / PROPOSED, with SEARM not reviewed, C-4PO not started, and disclosure internal-only.
- Canonical epistemic/workflow enums still need a deliberate reconciliation review; no silent change to the universal state machine is intended.
- The baseline control example makes no new gravity discovery claim.
- No schema validator, unit tests, scientific review or IP/legal review has been run as part of this documentation write.
