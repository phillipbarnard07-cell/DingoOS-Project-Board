# DingoOS IP proof-governance v1 — board update

**Date:** 2026-10-10  
**Main repository:** phillipbarnard07-cell/DingoOS  
**Branch:** integration/all-github-repositories  
**Status:** Governance specification, register, schema, validator and regression tests committed; runtime tests pending.

## Delivered
- docs/ip/DINGOOS-IP-ENGINEERING-AND-PROOF-GOVERNANCE-V1.md — asset identity, provenance, disclosure gate, threat controls and legal/epistemic limits.
- schemas/ip-asset-register-v1.schema.json — strict machine-readable register contract.
- ip/register.v1.json — Horizon adapter inventory pointer, deliberately marked ownership UNASSESSED, contribution UNKNOWN, third-party review NOT_STARTED, disclosure NOT_REVIEWED.
- scripts/validate_ip_register.py — dependency-free fail-closed structural validator.
- tests/test_ip_register.py — regression coverage for unknown fields/enums, duplicate IDs, dates/digests and disclosure blockers.
- progress/2026-10-10-ip-proof-governance-v1.md — implementation and verification status.

## Verification truth
The test cases have been authored but were not executed in this environment. Do not treat source commits as passing tests. The validator establishes only structural/project-control consistency, not legal ownership, patentability, novelty, freedom to operate or scientific truth. No paid service or CI billing gate was added.

## Next
Run:
python -m unittest discover -s tests -p 'test_ip_register.py' -v
python scripts/validate_ip_register.py ip/register.v1.json

Then produce exact artifact digests for release candidates, inventory third-party code/data/models, verify public/private repository boundaries, and obtain qualified review for ownership/licensing questions before any public release.
