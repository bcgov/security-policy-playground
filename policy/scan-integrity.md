# Scan Integrity and Coverage

Status: **proposed security requirement**, not adopted, not enforced. Applies in every phase.

Cross-cutting. Applies to every control rather than to one draft.

## Control

A passing security check means the control ran and evaluated the expected scope.

## Policy

- The following must be distinguishable from one another and must not be collapsed: applicable, not applicable, applicability undetermined, executed with no findings, executed with findings, execution failed, timed out, no result produced, malformed result, and skipped.
- Control execution and evidence integrity are never advisory, in any phase.
- A control that did not run must not be recorded as satisfied.
- Not applicable must be declared by the control, not inferred from its absence.
- A centrally governed control must not be satisfiable by a job the constrained repository defines for itself.

## Technical requirements

- A job skipped by a condition reports success to the merge gate and does not block a merge, including where it is a required check. Merge enforcement therefore does not depend on a check run produced by a job the constrained repository can condition.
- Each run records the targets discovered, the targets evaluated, the exclusions applied, and the tool and policy versions. Where relevant targets are present and none were evaluated, the control fails.
- Execution failure, timeout, a missing result and unparseable output fail the check whether or not findings block.
- Advisory behaviour is expressed through the scanner's own non-blocking setting. A step-level error suppression must not be used for this purpose, because it also hides failures unrelated to findings.
- Each central control is enforced individually by the organization rule that requires its named workflow to pass. No job defined by the constrained repository stands between a control result and the merge gate.
- A control that does not perform its analysis declares not applicable in its own machine-readable run record and still completes as a run. Applicability is evaluated in estate coverage reporting, not at the merge gate.
- Merge enforcement binds to the identity of the producing central workflow through the organization rule that requires that workflow to pass, so a job name alone cannot satisfy it. That rule requires the workflow's source repository to match the visibility of the repositories it runs in, so the control repository serving public repositories is public and holds no content that must not be public.
- A control that requires privileged access is not satisfied by declaring not applicable on a pull request from a fork. It runs on the merge commit through the merge queue event, after review and before merge. It does not run against the fork head ref in a privileged context. Controls that do not require privileged access run on fork pull requests normally. Where a repository accepts fork contributions and a required privileged control cannot run on the merge group, the gap is recorded as a coverage exception and is not resolved by refusing all fork pull requests.
- A control removed from an aggregating check is recorded as an open coverage gap with the date it was removed and the reason.
- Coverage is reported across the estate monthly, identifying repositories that are in scope and have produced no result and repositories with no security configuration attached, so a control that was never adopted is detected as well as one that failed. An unattached configuration is a failed control, not an absent one. The coverage report is not published to a public location, because it names which repositories are currently unprotected. A finding in one repository is raised where that repository's work happens; the estate-wide view of which repositories have no coverage is not.

## Implementation

Centrally maintained reusable workflows for the per-run record. Organization rules for merge enforcement. Central reporting for estate coverage.
