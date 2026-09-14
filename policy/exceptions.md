# Exceptions and Suppressions

Status: **proposed security requirement**, not adopted, not enforced. Applies in every phase.

Cross-cutting. Applies to every control rather than to one draft.

## Control

A suppressed finding is a recorded decision with a stated reason, an accountable owner and an expiry. It is not the absence of a finding.

## Policy

- An exception must identify what it suppresses, where it applies, why, who accepted it, who approved it, and when it expires.
- An exception is granted against a rule or a specific finding.
- A path exclusion is an exception and carries the same record. It is not a configuration detail outside the process.
- Repository-local configuration must not weaken a centrally governed control.
- An expired or unverifiable exception must not suppress a finding.
- A suppressed finding must remain visible.

## Technical requirements

- The central workflow runs the scanner against an explicit configuration path it supplies. Before the run it asserts that no other configuration or ignore file the approved scanner would discover is present in the evaluated tree. The filenames that count are maintained centrally against the scanner version, and an unlisted file that is present fails the check. A present file that is listed must correspond entry for entry to approved exception records.
- Suppressions written into the scanned content itself are detected rather than honoured. Before evaluation the central workflow scans the target set for the approved scanner's inline suppression syntax and fails the check on any occurrence, because no scanner setting disables inline suppression. The syntax, and the content formats each form applies to, are held centrally against the pinned scanner version rather than restated per repository, since both change with the scanner. For the two candidates that means `trivy:ignore` comments and `checkov:skip` annotations, and the recorded scope for each is what the pre-scan is built from.
- A suppressed finding is retained in the result set and names the exception that applies to it. Suppression changes whether a finding counts against policy, not whether it can be seen.
- The central workflow enables the scanner's suppressed-results output where the scanner provides one, so a suppressed finding appears in the result set rather than being dropped from it.
- Exclusions are expressed in the mechanism that implements them. A path belongs in the scanner's path exclusion setting, and an identifier belongs in the identifier list. An entry placed in the wrong mechanism is silently ineffective and presents as configured.
- An exception carries an absolute expiry date no more than 90 days after approval, renewable through the same approval path. The register is evaluated daily. An expired entry stops suppressing on the next control run and is reported as an open finding.
- Platform-driven exceptions are held once centrally and consumed rather than copied into each repository.
- Findings for which no fix is available are recorded and dated.
- A repository that cannot satisfy a control at all carries a scope exception rather than a set of finding exceptions. A scope exception is granted against a named control for a named repository, carries the same fields and expiry, is consumed by coverage reporting rather than by the scanner, and is reported as a state distinct from no result.
- Exception records are retained for audit for a stated period after expiry.

## Implementation

A central exception register, consumed by the reusable workflows at run time. Repository-local configuration has no effect on a central control except through an approved record.
