# Phase 2.3.1 — Ruleset Enhancement Proposal

Status: proposal only. This document does not change the active GitHub Ruleset.

## Scope and safety
- Target ruleset: `Abdulqawiqaid` (ID `26697504`), targeting `refs/heads/main`.
- Current ruleset was read successfully on 2026-11-10.
- This proposal is intentionally documentation-only. Do not apply settings through the GitHub UI/API as part of this PR.
- This branch is based on the Phase 2.3 governance branch so the proposals are reviewed in sequence. PR should target that branch, not `main`.

## Verified current ruleset
- Enforcement: active.
- Prevent branch deletion: enabled.
- Prevent non-fast-forward updates: enabled.
- Require pull requests: enabled.
- Required approving review count: 0.
- Required review-thread resolution: disabled.
- Required status check: `Verify canonical theme archive` (strict policy enabled).
- Bypass actor: repository role ID 5, bypass mode `always`; current user can bypass.
- Required status check `CodeQL` is not currently in this ruleset.

## Proposed changes for owner review

### 1. Require review-thread resolution
Set `required_review_thread_resolution = true`.
This is a low-friction safeguard and should be enabled once approved.

### 2. Add CodeQL as a required status check
Add the exact context `CodeQL` alongside `Verify canonical theme archive`, retaining strict required checks.
Evidence: PR #9's CodeQL check completed successfully and reported no new alerts in files changed by that PR. This does not certify the whole repository as vulnerability-free. Reconfirm the exact check context on a fresh PR before enforcing it.

### 3. Approval requirement — conditional
Recommended value: `required_approving_review_count = 1` only if at least one independent reviewer is available and can review routinely.
If this is a single-maintainer repository with no independent reviewer, retain `0` temporarily; GitHub does not allow an author to approve their own PR, so requiring one approval can block the owner's changes. Revisit when a reviewer is appointed.

### 4. Bypass policy
Review the existing repository-role bypass actor with `always` mode. Prefer minimizing bypass actors and using a narrowly scoped, documented emergency bypass process. Do not remove owner recovery access without a tested recovery plan. This PR does not change bypass configuration.

## Proposed acceptance criteria
- [ ] Owner confirms an independent reviewer exists before setting approval count to 1.
- [ ] CodeQL check context is reconfirmed on a fresh PR.
- [ ] Review-thread resolution behavior is understood and approved.
- [ ] Bypass actor and recovery path are reviewed.
- [ ] Owner applies any approved changes manually in repository Settings in a separately authorized action.
- [ ] Verify the active ruleset after any separately approved change.

## Explicit exclusions
No changes to active rulesets, branch protection, repository permissions, Actions permissions, secrets, workflows, canonical Blogger XML, manifest, DNS, Blogger, AdSense, Analytics, consent settings, or production deployment.
