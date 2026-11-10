# Security Policy

## Scope
This repository maintains a historical Blogger XML theme archive and associated documentation. It is not the live Blogger deployment pipeline. The archived theme is not a supported software release and has documented unresolved security risks.

## Reporting a vulnerability
Please report vulnerabilities privately using GitHub's **Report a vulnerability** option under the repository Security tab, **if private vulnerability reporting is enabled**. If that option is unavailable, contact the repository owner through an established private channel. Do **not** disclose exploit details, credentials, tokens, or personal data in public issues or pull requests.

Do not submit secrets or patient information. Provide a concise description, affected file or behavior, reproduction steps that do not harm users, and potential impact. No response-time or remediation SLA is currently promised.

## Supported versions
No versioned release line or security-support commitment is currently defined. The canonical XML is preserved as a historical reference, not certified as safe for production use.

## Handling and disclosure
Reports should be triaged privately, with a fix proposed on a separate branch and reviewed before any merge. Coordinate disclosure after remediation when appropriate. Never test against live users or production systems without explicit authorization.

## Known risk
See [the static audit](sites/abdulqawiqaid.org/audits/2026-11-06-static-audit.md) and [remediation plan](sites/abdulqawiqaid.org/audits/2026-11-06-remote-code-remediation-plan.md). The documented remote JavaScript execution design is a risk, not proof of compromise.
