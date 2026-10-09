# DingoOS Runner Execution Recovery — Self-Hosted Path

**Date:** 2026-10-10  
**Status:** Workflow and runbook committed; actual execution awaits an online, configured self-hosted runner.  
**Canonical blocker:** [Issue #53 — P0: restore observable GitHub Actions runner execution for certification](https://github.com/phillipbarnard07-cell/DingoOS/issues/53).

## Reassessment

Existing workflow-run inspection confirms the recurring runner-probe failure is not evidence of source/test failure: run [37479382569](https://github.com/phillipbarnard07-cell/DingoOS/actions/runs/37479382569) is completed with failure, while the job endpoint returns `steps: null` and `logs_url: null`. The repository has a known billing/spending restriction for GitHub-hosted runners. Automatic triggers and paid hosted execution are not an acceptable workaround.

## Implemented

A manual-only, self-hosted workflow is now on the default branch so the Actions UI can dispatch it:

- Workflow: https://github.com/phillipbarnard07-cell/DingoOS/blob/main/.github/workflows/foundation-self-hosted-verification.yml
- Runbook: https://github.com/phillipbarnard07-cell/DingoOS/blob/main/docs/operations/FREE-SELF-HOSTED-FOUNDATION-VERIFICATION.md
- Workflow commit: `bf2889575712b378329dbca52c455fa7de3af1c8`
- Preflight hardening: `dc52f6621ec4b3b3dd8d8bd62e55c06ce92c6302`
- Artifact/final-gate enforcement: `f3c75e0a5c0d0f1d10ae7c1b762932f308845e48`
- Runbook commit: `cdfbaa7858a80884b6f3f4cd3ecfa4aa61603dd9`

Workflow properties:
- `workflow_dispatch` only; no automatic push or pull-request triggers.
- `runs-on: self-hosted`; never allocates a GitHub-hosted runner.
- Requires an independently copied full lowercase 40-character SHA and checks the checkout matches.
- Creates a named local verification branch so the canonical runner does not run from detached HEAD.
- Runs the existing foundation compile/gate/test sequence without installing dependencies.
- Validates the generated report against the exact checkout and inventoried source bytes.
- Prints a summary without echoing captured stdout/stderr, retains one JSON report for one day, and requires both verification and evidence retention for a final PASS.
- Read-only repository permission; no secrets; no scientific-truth or deployment authorization claim.

## Required operator action

A repository owner must register and keep online a self-hosted runner at **Settings → Actions → Runners → New self-hosted runner**. Python 3.11+ and the target revision's test dependencies must already be available locally; the workflow does not install packages. Then open **Actions → DingoOS Free Self-Hosted Foundation Verification → Run workflow**, enter the current full candidate SHA, and inspect the result and report.

If no self-hosted runner is online, the job will remain queued. No execution or passing test result is claimed until the job actually runs and its report/logs are reviewed.

## Governance disposition

PR #69 remains unpromoted until source-bound verification evidence is available. A successful software test run would not itself establish scientific validity, legal/IP clearance, production readiness, or consequential deployment authorization. Production remains **NOT VERIFIED / NO-GO / HOLD**. No paid workflow was triggered.


Privacy clarification: the one-day JSON artifact contains captured command stdout/stderr, which may include private paths or test-generated data; treat it as private evidence and inspect before sharing. Runbook clarification commit: `a17ee06234a41d35047894e8e295ffed5cd8f071`.

## Follow-up source-level hardening — 2026-10-10

After re-reading the canonical runner and evidence validator on PR #69's candidate branch, the workflow's CLI arguments were confirmed to match the validator interface. The scripts are intentionally absent from `main` but present on the candidate revision, so dispatch must use that exact current candidate SHA.

Additional defense-in-depth committed to the default-branch workflow:
- Final gate independently requires both report revision fields to equal the operator-supplied SHA.
- It independently requires source revision stability, repository/branch stability and clean pre/post-run worktree claims.
- It explicitly enforces `claims.paid_github_ci_required == false`, preserving the project owner's no-paid-CI boundary.
- It continues to require the evidence validator and artifact upload to succeed, plus `result == PASS`.
- It does not install packages, use GitHub-hosted runners, or dispatch itself.

Workflow hardening commit: `3efc18bbf642c61e520730c6442cb0637be06a01`.

**Evidence status unchanged:** source-level interface review only. No workflow dispatch, runner execution or tests were performed in this session. The workflow is not runtime-verified until an owner-operated self-hosted runner is online and the exact-revision run is inspected. Decision remains **NOT VERIFIED / NO-GO / HOLD**.
- Follow-up runtime compatibility hardening: workflow now enforces Python 3.11+ before running the foundation suite and records the interpreter path/version. Commit: `f50918a76a0ecdaef9819ba74121d5496a1919cb`.

