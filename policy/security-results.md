# Security Results

Status: **proposed security requirement**, not adopted, not enforced. Applies in every phase.

Cross-cutting. Applies to every control rather than to one draft.

## Control

Findings reach one predictable place, carry enough detail to act on, and remain attributable to the producer that generated them.

## Policy

- A finding must identify what was detected, where, how severe it is, and what to do about it.
- A result must identify what produced it, what it evaluated, and when.
- Results from different producers must remain distinguishable.
- A control must not convert output into a format the tool does not support in order to place it in a particular view.
- Externally produced findings must be identifiable as such and must have a named internal owner.

## Technical requirements

- A finding records the rule or advisory identifier, a title and description, severity and the source of that severity, the affected file and line or the affected resource, dependency, endpoint or image, and remediation guidance.
- A run records the tool name and version, the policy or ruleset version applied, the commit or artifact evaluated, the execution time, and whether the control was applicable.
- Where a control compares two commits, each finding carries a disposition of introduced, unchanged or resolved, derived from a stable fingerprint, with both commits recorded.
- Every upload to code scanning sets an explicit category identifying the producer and the analysis purpose. A direct API upload sets a unique run automation identifier instead. An upload with neither receives a generated identity derived from the workflow file path, the job identifier and any matrix values, so renaming the workflow file, renaming the job or adding a matrix dimension starts a new series of alerts and abandons the previous one.
- A category carries one analysis purpose. Where one tool performs several kinds of analysis, each is published separately rather than combined into one result.
- The publishing step counts results before upload. A run producing more than 5,000 results fails the check rather than uploading, because code scanning retains only the top 5,000 by severity and the loss is silent. An upload rejected for exceeding a hard limit, currently 10 MB compressed, 25,000 results per run, 25,000 rules per run or 20 runs per file, fails the check and is not retried with a reduced result set.
- Externally produced findings are published under a tool name and category that identify the producer, and are adjudicated to true or false positive by a named internal owner before they are actioned or closed.
- A workflow log is not evidence. Machine-readable results are retained as an artifact or in an approved evidence store, with the retention period stated.

## Implementation

GitHub-native code scanning for storage and presentation. Centrally maintained reusable workflows for normalisation and publication.
