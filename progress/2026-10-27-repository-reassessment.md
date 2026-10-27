# DingoOS repository reassessment — 27 October 2026

This is a public-safe, revision-pinned snapshot. It is an inventory and planning estimate, not proof of runtime acceptance.

## Identity and size

- Repository: https://github.com/phillipbarnard07-cell/DingoOS
- Candidate PR #237: https://github.com/phillipbarnard07-cell/DingoOS/pull/237
- Inspected candidate: `c4a0c20e58407e34c1e3395f4e6b3241326da66f`
- Tracked files: 953; directories: 118.
- Tracked file bytes: 5,670,464 (5.41 MiB).
- GitHub repo size metadata: 7,245 KiB (~7.1 MiB), a different measure.
- 524 Python files; 288 paths in `tests/`; 256 in `docs/`; 109 paths in `core/`; 66 in `engineering/`; 52 in `schemas/`; 28 in `scripts/`; 10 JavaScript files; 3 HTML files; 3 shell scripts; 21 YAML files.
- Exact LOC: NOT MEASURED. Git tree exposes byte sizes, not line counts. The historic 5,449 additions in PR #45 are not current repository LOC.

## Provisional module code-maturity estimates

These are manual low-confidence estimates of source-level implementation presence, not verified completion. Integration and runtime evidence remain separate.

| Module | Estimate | Main qualification |
|---|---:|---|
| Core primitives, state, contracts | 60% | Canonical-path verification pending |
| Schemas / typed records | 70% | Service alignment pending |
| Research API / ledger / persistence | 55% | Real journey/restart not verified |
| Horizon / Study Buddy UI | 65% | Browser/API end-to-end not verified |
| C-2PO objectives / intake | 50% | End-to-end traceability pending |
| C-3PO proposal / orchestration | 40% | Provider/resource qualification gates pending |
| C-4PO challenge / refutation | 50% | PR #241 source exists; full tests NOT RUN |
| SEARM qualification / replication | 40% | Persisted qualified-result path not verified |
| Evidence / provenance / append-only ledger | 50% | Canonical wiring and tamper/recovery pending |
| DDEP execution / workflow / recovery | 40% | Distributed fencing/revocation gaps; 0/74 stages certified |
| EPC / mathematics kernel | 45% | Integrated transition path not verified |
| Knowledge Snowflake / admission | 35% | Canonical admission integration pending |
| Security / authorization / local boundaries | 55% | Comprehensive runtime/adversarial verification pending |
| Tests / acceptance harness | 45% authored-source maturity | Full current-candidate test run NOT RUN |
| Build / reproducibility / operations | 30% | Genuine npm lockfile and runner evidence pending |
| Documentation / architecture / traceability | 75% | Requires live evidence reconciliation |

The single ~50% source maturity figure is a planning signal only. A formal weighted completion percentage remains UNMEASURED until the census and evidence-backed reassessment are reproducible.

## Current status

- PR #237 remains draft/unmerged; POC acceptance HOLD.
- Full current-candidate suite and real process restart/tamper acceptance have not been demonstrated.
- Four noisy workflow definitions have been preserved under `.github/paused-workflows/` on the PR #237 branch after failed push runs exposed zero jobs. Verify that runs stop before re-enabling them.
- No merge, release, production deployment or consequential dispatch is authorized.

## Next gates

1. Exact-SHA LOC/file/function/test census.
2. Genuine reviewed npm lockfile.
3. Authorized local-runner execution with raw logs and exit codes.
4. Persistence/restart, correction/supersession and tamper rejection.
5. Canonical C-4PO/SEARM/evidence/provenance/Knowledge integration.
6. Independent review and explicit POC acceptance.
