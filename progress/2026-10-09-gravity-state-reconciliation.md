# Progress — Gravity Hypothesis State Reconciliation

**Date:** 2026-10-09  
**Status:** Canonical vocabulary reconciliation committed; source files re-fetched for verification. Runtime pytest and full JSON Schema validation have not been executed.

## Change
The gravity domain schema previously used an independent `epistemic_class` vocabulary. It now uses the canonical DingoOS vocabularies from `evidence/epistemic.py`:
- `epistemic_state`: canonical transition state;
- `epistemic_label`: orthogonal descriptive label;
- `research_classification`: domain classification such as `SPECULATIVE_PHYSICS`;
- `status`: remains aligned with the generic `schemas/research/hypothesis.schema.json` workflow status.

The baseline control record now starts at PROPOSED / HYPOTHESISED / SPECULATIVE_PHYSICS, with SEARM NOT_REVIEWED, C-4PO NOT_STARTED and IP release INTERNAL_ONLY. Conditional schema rules now require evidence references and completed SEARM/C-4PO review artifacts for VALIDATED_FOR_SPECIFIED_DOMAIN and REFUTED states, with aligned canonical labels.

## Added regression tests
`tests/research/test_gravity_hypothesis_contract.py` checks:
- gravity state and label enums match the canonical Python enums;
- generic workflow status matches the existing hypothesis schema;
- baseline record begins in the required conservative states;
- the canonical state machine blocks direct promotion shortcuts and keeps REFUTED terminal;
- terminal-state conditional schema requirements are present for evidence, SEARM decision, C-4PO review, derivation review and aligned label.

## Verification boundary
GitHub content retrieval and JSON parsing were performed during authoring. The pytest test file was not executed; no full JSON Schema validator was run. Conditional schema rules are structural safeguards, not substitutes for transition-service authorization or a persisted SEARM decision. No scientific, patentability or legal-ownership conclusion is implied. No paid CI or service was added.
