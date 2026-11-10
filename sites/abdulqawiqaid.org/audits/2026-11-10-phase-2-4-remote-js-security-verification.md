# Phase 2.4 — Remote JavaScript Security Verification

**Review date:** 2026-11-10  
**Repository:** `abdulqawiqaid/abdulqawiqaid.org`  
**Source branch baseline:** `main` at `1c6a517129a7cf0ff0b60a2f0a88229542b2fbdc`  
**Canonical theme:** `theme-3351920114810663100 (2) (15).xml`  
**Git blob:** `6826a09292ac3882efde09f63de0d06d1257f0c6`  
**Review mode:** static, non-executing  
**Production:** unchanged

## Executive summary

The archived theme still contains a high-risk remote-code execution path. The current static source confirms that a Blogger feed callback named `sp_db` parses remote Blogger page HTML, extracts a script's text, stores that text in session storage under `storg`, and passes it to `eval`. The lazy-loading path also evaluates the cached value.

This is a critical design weakness: remote editable page content can become JavaScript executed in the visitor's site origin. It is not proof that the remote page is currently malicious or that the site has been compromised.

The current remote Blogger page could not be retrieved during this review. Consequently, the present remote payload, its ownership, nested behavior, and any changes since the historical snapshot remain unverified. No blind code removal or production remediation was attempted.

## 1. Source integrity and review method

- The repository tree identifies the canonical theme as a 391,267-byte XML blob with Git blob SHA-1 `6826a09292ac3882efde09f63de0d06d1257f0c6`.
- This matches the canonical blob identity recorded in the previous static audit.
- The XML was inspected as text only. No JavaScript was executed.
- The review searched for the remote callback and storage identifiers, dynamic execution, script loaders, external script hosts, HTML sinks, and comment integrations.
- The public Blogger feed endpoint associated with the previously documented page ID was not accessible through the available fetch path. Current endpoint content therefore remains an explicit evidence gap.

## 2. Findings

### F-24-01 — Remote Blogger page content is evaluated as JavaScript
**Severity:** Critical / P0  
**Status:** Confirmed in archived theme source

The obfuscated theme defines `sp_db`. Its visible data flow is:
1. Receive a Blogger feed callback object.
2. Parse the returned page HTML using `DOMParser`.
3. Select a script element and read its text.
4. Store the extracted text in `sessionStorage` under the `storg` key.
5. Execute the extracted text with `eval`.

The exact remote page ID and request URL are obscured by string-decoding logic in the theme source. The prior audit records the historical page ID as `137042360492087078` and the associated blog ID as `5637748011392113640`; these identifiers were not independently decoded from the current XML in this pass.

**Impact:** an editor or attacker able to influence the remote page or its delivery could supply code that executes in the site's origin.

**Required action:** remove the remote HTML-to-script extraction and evaluation design. Migrate only verified required functions into readable, versioned, locally controlled code. Treat remote responses as data, validated against a strict schema; never evaluate configuration strings.

### F-24-02 — Cached remote script is evaluated again
**Severity:** Critical / P0  
**Status:** Confirmed in archived theme source

The `LazMin` path checks the `storg` session-storage value and evaluates it. The lazy-loading flow also contains a further conditional evaluation of the same stored value.

**Impact:** the remote executable payload persists across page activity within the session and can be replayed by separate execution paths.

**Required action:** remove the executable session-storage cache and all replay paths. Session storage must not be treated as a trusted code store.

### F-24-03 — Dynamically loaded third-party scripts
**Severity:** High / P1 review  
**Status:** Confirmed; trust and runtime necessity not fully verified

The archived theme includes or dynamically loads:
- Google AdSense from `pagead2.googlesyndication.com`.
- Google Charts from `www.gstatic.com/charts/loader.js` for the Blogger stats widget.
- Disqus embed code from a host assembled using the `disqus-id` metadata value.
- Facebook SDK from `connect.facebook.net`.

The source also has a generic helper that creates a script element and assigns its `src`. A complete runtime host inventory and verification of the Disqus metadata value, Facebook app configuration, and actual network requests remain outstanding.

**Required action:** document each retained dependency's owner, purpose, exact host, loading condition, and integrity/control model. Prefer fixed, reviewed host allow-lists and avoid constructing executable URLs from untrusted values. Test comments and advertising before changing any integration.

### F-24-04 — HTML injection sinks need source-to-sink review
**Severity:** High / P1 review  
**Status:** Confirmed sinks; exploitability not established

The source contains multiple assignments to `innerHTML` and `outerHTML`, including transformations of post content and custom link markup. It also contains `insertAdjacentHTML` uses.

These observations are not a vulnerability count and do not establish that each sink is exploitable. The next review must trace whether each value is trusted template content, Blogger-authored post content, query/storage input, or remote response data.

**Required action:** use `textContent` for text, DOM construction for elements, and a maintained allow-list sanitizer only where HTML is intentionally required. Validate URL schemes and destinations before creating links.

### F-24-05 — Obfuscated JavaScript prevents reliable source review
**Severity:** High / P1  
**Status:** Confirmed

Large sections of the theme's JavaScript use obfuscated identifiers and encoded string tables. This makes endpoint identification, dependency review, and call-graph analysis less reliable.

**Required action:** obtain readable upstream source or perform non-executing deobfuscation in an isolated analysis environment. Do not run unknown code in a logged-in browser or on the production origin.

### F-24-06 — Redirect and remote HTML behavior remains unverified
**Severity:** High / P1  
**Status:** Partially confirmed from historical audit; current remote implementation unverified

The previous audit documented a redirect preview flow that fetches remote HTML and extracts script content for evaluation. The current archived theme includes obfuscated redirect-related code, but the remote page and nested payload could not be retrieved for current verification.

**Required action:** inspect the exact current remote response and trace all redirect inputs. Parse destinations with `URL`; allow only explicit protocols and approved hostnames; reject credentials, `javascript:`, `data:`, protocol-relative and malformed destinations. Render remote previews as inert sanitized content and never execute scripts from fetched HTML.

### F-24-07 — Inline execution and JavaScript URL patterns
**Severity:** Medium / P2  
**Status:** Confirmed in static scan

The archived theme includes inline event handlers and a `javascript:` URL for the print action. These patterns complicate a restrictive Content Security Policy and should be migrated to unobtrusive event listeners and semantic controls when functional regression testing is available.

## 3. Evidence gaps and blockers

1. Current source of the remote Blogger page was not retrievable in this review.
2. The live feed request URL and page ID are obscured by obfuscated string-decoding code and require safe, non-executing resolution.
3. Historical nested eval behavior cannot be treated as current without the current remote response.
4. No live browser network trace, storage inspection, or private Blogger test-blog run was performed.
5. No independent verification of third-party script ownership, current versions, or runtime necessity was completed.

These gaps block safe code remediation. Do not remove the loader or replace `eval` with a dynamically inserted script element as a superficial fix.

## 4. Required remediation sequence

1. Obtain a fresh export or exact current feed response for the remote Blogger page, preserving timestamp and source URL.
2. Decode the request construction and remote identifiers without executing the theme's obfuscated code.
3. Compare the current remote payload with the historical snapshot and identify all external hosts and script blocks.
4. Build a function inventory and call graph. Classify each function as required, unused, third-party, or unknown.
5. Reimplement only required behavior in readable, versioned code controlled by the repository or an explicitly controlled static host.
6. Replace executable remote HTML with strict JSON data validation. Remove `storg`, remote script extraction, `eval`, and replay paths.
7. Review HTML sinks and redirect handling; test hostile markup, encoded URLs, nested URLs, and unapproved hosts.
8. Create a candidate under a separate directory. Do not overwrite the canonical XML.
9. Validate XML well-formedness, source diff, dependency inventory, redirect tests, and static scans.
10. Test only in a private Blogger test blog. Cover homepage, posts, pages, labels, search, comments, pagination, lazy loading, redirects, Consent Mode, AdSense placeholders, mobile/desktop, accessibility, console errors, network requests, and storage.
11. Keep the PR draft until evidence and the regression matrix are complete.

## 5. Explicit exclusions

This review did not:
- Modify the active canonical XML or manifest.
- Modify Blogger pages, widgets, or settings.
- Change DNS, Cloudflare, AdSense, Analytics, consent settings, or production.
- Execute any remote or obfuscated JavaScript.
- Remove or replace any script loader.
- Merge a pull request or publish a deployment.

## 6. Exit criteria

- No executable JavaScript is fetched from Blogger page/post HTML and evaluated.
- No script extracted from widget output or redirect HTML is executed.
- No remote executable payload is stored and replayed from browser storage.
- Every retained third-party dependency has an identified owner, purpose, exact host, and loading condition.
- Redirect destinations are explicitly validated and previews are inert.
- HTML sinks have documented source-to-sink analysis and appropriate safe rendering.
- Required behavior passes the private Blogger test matrix.
- Canonical archive blob identity remains unchanged.

## Rollback

The canonical XML remains untouched. All future remediation must be isolated in candidate files and tested in a private Blogger test blog. Restoring the archived original theme is the test-environment rollback path. Any production upload or Blogger page change requires separate explicit approval.
