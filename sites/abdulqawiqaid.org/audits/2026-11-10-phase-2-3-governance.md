# Phase 2.3 — Repository Governance & Security Hardening

Status: proposed; no repository settings or production settings changed.

## Observed baseline
- Default branch: `main`; canonical archived Blogger XML remains unchanged.
- Validation workflow runs on PRs targeting `main` and manual dispatch, with `contents: read`.
- Workflow uses `actions/checkout@v4` (major-version tag, not an immutable SHA).
- Current validation checks manifest byte length, SHA-256, Git blob SHA-1 and XML well-formedness only.
- Branch protection, rulesets, CodeQL coverage, secret scanning and latest workflow-run status require separate confirmation via authorized settings/Actions views.
- No live-site security, SEO, AdSense or consent verification is implied by CI.

## Recommended repository settings (owner approval required)
1. Protect `main` against force pushes and deletion.
2. Require pull requests and the **observed exact** validation status-check name before merging. Verify the check runs on all PRs before making it required.
3. Require resolution of review conversations; configure approvals only if an independent reviewer is available.
4. Restrict Actions token defaults to read-only, allow elevated permissions only for explicitly reviewed jobs.
5. Review allowed Actions and pin reviewed third-party actions to full commit SHAs in a separate PR; keep the current workflow working.
6. Enable private vulnerability reporting and appropriate secret scanning/security alerts if supported.
7. Never enable Blogger deployment or update DNS, AdSense, Analytics, or production theme as part of governance work.

## Review checklist
- [ ] Inspect repository rulesets and branch protection directly in GitHub Settings.
- [ ] Confirm last five workflow runs and identify the exact required check name.
- [ ] Review Actions permissions, triggers and all action references.
- [ ] Confirm CodeQL/secret-scanning coverage and alert access.
- [ ] Review SECURITY.md for a viable private reporting channel.
- [ ] Verify unchanged canonical XML and manifest.
- [ ] Obtain explicit owner approval before changing repository settings or merging.

## Rollback
Revert this documentation-only PR if needed. The original theme and validation workflow are unchanged.
