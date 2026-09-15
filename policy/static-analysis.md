# Static Analysis

Status: **proposed security requirement**, not adopted, not enforced. Evaluation phase.

Requirements for the control drafted in [sast-and-code-quality.md](../sast-and-code-quality.md). That draft records the current state and the gap; this document states the requirement.

## Control

Source code is analysed for security defects before merge to a protected branch.

## Policy

- Every repository in scope with a supported language must be analysed on pull requests targeting a protected branch and on the default branch.
- Findings are advisory during the evaluation phase. What is never advisory is listed in [scan-integrity](scan-integrity.md). Analysis failure, timeout and missing results are not advisory.
- Code quality analysis must not be counted as security analysis coverage.
- A second static analysis product must not be introduced for a language already covered, absent a measured coverage gap.

## Technical requirements

- Analysis is enabled centrally rather than by each repository adding configuration.
- Default setup and advanced setup are mutually exclusive for a repository, not for a language. Enabling default setup disables an existing CodeQL advanced setup workflow and rejects CodeQL result uploads from it, for every language and not only the overlapping one, so a repository that needs advanced setup for one language uses advanced setup for all of them. SARIF produced by a tool other than CodeQL is not affected and continues to upload. Advanced setup is justified in the change that introduces it.
- The organization security configuration sets code scanning to enabled with advanced setup allowed. A configuration that requires default setup does not attach to a repository already running advanced setup, so a repository that adds an advanced setup workflow drops out of the configuration and stops being enforced by it.
- Where advanced setup is used, the workflow schedules analysis at least weekly against the default branch, matching the cadence default setup provides. A repository with infrequent commits does not go unanalysed.
- Language coverage is recorded with the result. A repository whose language is unsupported must be distinguishable from one that was analysed and found clean.
- Results are published to code scanning, with the finding content required by [security-results](security-results.md).
- The blocking threshold is high and critical. It is enforced by the organization code scanning rule keyed to the analysis result, not by an exit code inside a workflow the repository can edit. Enforcement is switched on at the end of the evaluation phase; the threshold does not change at that point.
- Where the control depends on a licensed feature, a repository without that licence is reported as outside coverage rather than as clean.

## Implementation

Organization security configuration for enablement. A centrally maintained reusable workflow only where advanced setup is justified.
