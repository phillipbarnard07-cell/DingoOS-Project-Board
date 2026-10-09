# E-HOVER-01 Background Propulsion Evaluation v1.0

**Date:** 2026-10-09  
**Status:** Registry and typed contract committed; source-level structural checks performed. Runtime tests and full JSON Schema validation remain unexecuted.

## Added artifacts
- `schemas/e-hover-01/background-propulsion-candidate-v1.schema.json`
- `schemas/e-hover-01/background-propulsion-candidates-v1.json`
- `docs/advanced-systems/E-HOVER-01-BACKGROUND-PROPULSION-EVALUATION-V1.md`
- `tests/test_ehover01_background_propulsion.py`

## Coverage
Eleven candidate mechanisms are recorded separately: distributed electric propellers, ducted fans, lift-plus-cruise/tilting propulsion, ground-effect wing, air cushion, magnetic levitation, electrohydrodynamic/ion wind, buoyancy, reactionless-field claims, toroidal-resonance hypotheses, and vacuum-energy/Casimir propulsion hypotheses.

Each record includes energy source, reaction medium/external infrastructure, assumptions, equations with epistemic status and units, discriminating test, falsifiers, limitations, electrical architecture (actuator/motor, conversion, distribution, monitoring, isolation), hazards, evidence references and provenance.

## Engineering boundary
The comparison prioritizes measurable conventional mechanisms. Toroidal geometry and Casimir forces are not treated as propulsion mechanisms by themselves; reactionless propulsion receives no engineering credit without a reproducible, controlled force and energy/momentum ledger. All entries remain BACKGROUND_ONLY; no hover capability or physical validation is claimed.

The note includes first-order thrust, actuator-disk power, current and endurance relations, while warning that real electrical sizing and vehicle safety require validated models and competent review.

## Verification boundary
GitHub files were re-fetched and parsed; a source-level check found all 11 registry records contained the schema's top-level required fields and nested electrical/provenance fields, with enum membership checks passing. The pytest suite was not run and no full Draft 2020-12 JSON Schema validator was run. No paid services or CI requirements were introduced.
