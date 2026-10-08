# DingoOS Project Board

Public, sanitized engineering control centre for DingoOS Pty Ltd.

**Board:** https://phillipbarnard07-cell.github.io/DingoOS-Project-Board/

This repository is deliberately separate from the private canonical DingoOS repository. It contains project-board information only: progress metrics, architecture maps, workstream status, milestones and public-safe links.

It does not mirror proprietary source code, private IP, credentials, protected governance implementation, security roots or internal infrastructure.

Implemented means executable code exists. Tested means automated tests cover behavior. Integrated means connected to the canonical runtime. Verified means evidence demonstrates the contract. Design documentation is never counted as implementation.

**Evidence Over Assumption · Science Over Belief · Provenance Over Assertion**

## Current engineering checkpoint — 2026-10-08

**Canonical integration:** DingoOS PR #69, branch `integration/all-github-repositories`.

**Current block:** scheduler → DDEP `ExecutionObject` binding → separate execution authorization gate.

**Implemented in the private canonical repository:**
- typed, schema-shaped `ExecutionObject` contract with deterministic digest and fail-closed status rules;
- scheduler `ComputeDecision` now preserves selected `resource_id`;
- DDEP can run in strict binding mode and durably record one `EXECUTION_OBJECT` per committed stage;
- execution authorization is now explicitly separated from scheduler/resource eligibility; strict DDEP execution fails closed without an explicit authorization;
- the authorization gate reuses the canonical `AuthorityRegistry`/`Authorization` primitives, requires matching resource and `AUTHORIZE` scope, and records the authorization reference/result on the ExecutionObject;
- restore verifies the durable execution binding against the scheduler decision;
- regression coverage includes malformed fields, fail-closed status, replay/idempotency and tamper detection.

**Verification state:** **HOLD / NOT VERIFIED**. The current execution environment cannot run the repository-local foundation runner because no usable checkout is available and network resolution for GitHub is unavailable. GitHub Foundation Gates is observable only as a completed failure with no executable steps/logs exposed (`steps: null`, `logs_url: null`); no source-code failure is inferred.

**Cost boundary:** no paid GitHub service is required or introduced.

**Current DingoOS head:** `4f84d3c2411042eab73263317b0884de836930f3`.

The board intentionally does not claim scientific validation, production certification, consequential deployment, or overall completion percentage.
