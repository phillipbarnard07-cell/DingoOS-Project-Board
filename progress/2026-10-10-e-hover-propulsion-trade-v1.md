# DingoOS progress — E-HOVER-01 propulsion trade v1

**Date:** 2026-10-10  
**Development branch:** integration/all-github-repositories  
**State:** Written and committed; automated tests not yet run.  
**Epistemic class:** BACKGROUND_ONLY.

## Delivered

- Added ehover01/propulsion_trade.py with fail-closed first-order calculations for weight, thrust target, ideal actuator-disk induced power, approximate electrical power/current and idealized endurance.
- Added a typed JSON Schema contract and explicit baseline comparison for EHV-P-01 open distributed propellers versus EHV-P-02 ducted fans.
- Missing inputs remain null and produce INCONCLUSIVE; no vehicle parameters were invented.
- Added unit tests for incomplete inputs, invalid efficiency, positive outputs and suppression of rankings unless both candidates are complete.
- Added an engineering note covering equations, uncertainty limitations, electrical-wiring boundary and DDEP gate position.

## Important boundaries

The actuator-disk relation is idealized; electrical power depends on supplied aggregate efficiency and is only a screening estimate. No uncertainty propagation, validated installed propulsor map, wiring approval, vehicle build, flight capability or physical performance is claimed. The current candidate comparison has no project-specific inputs and therefore remains INCONCLUSIVE.

## Verification status

Files were written to the repository through GitHub's contents API. The test suite and a full Draft 2020-12 JSON Schema validator have not been run in this environment. Do not treat commits or structural inspection as test evidence.

## Next

Connect this contract to the existing typed requirements/mass budget and source candidate-specific measured or qualified data; then add uncertainty-case evaluation without promoting model output to evidence.
