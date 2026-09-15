# Software Composition Analysis

Status: **proposed security requirement**, not adopted, not enforced. Evaluation phase.

Requirements for the control drafted in [supply-chain-and-containers.md](../supply-chain-and-containers.md). That draft records the current state and the gap; this document states the requirement.

## Control

Known vulnerabilities in third-party dependencies are identified before a dependency change merges, and remain visible afterwards.

## Policy

- Each dependency function has one owner: inventory, post-merge vulnerability visibility, pull request evaluation, and update pull requests.
- Two tools must not produce competing remediation pull requests for the same advisory.
- Findings are advisory during the evaluation phase. What is never advisory is listed in [scan-integrity](scan-integrity.md). A comparison that cannot complete is not advisory.
- A failed advisory lookup must not be reported as the absence of a fix.

## Technical requirements

- The dependency graph and continuous vulnerability alerting are both enabled explicitly. The graph alone produces inventory and no alerts, so enabling it is not sufficient.
- Pull request evaluation compares the dependency set at the base commit against the head commit, and distinguishes dependencies introduced by the change from those already present, and direct from transitive where the ecosystem supplies that information.
- Automated version and security update pull requests from the native updater remain disabled while the central dependency updater owns update pull requests. Vulnerability alerting remains enabled, since it is a separate setting and it is what the updater and the security views both consume.
- A dependency finding records package ecosystem and name, the versions at base and head, the advisory identifier, severity and the source of that severity, direct or transitive status, runtime or development scope where available, first patched version where one exists, licence, and a remediation reference.
- Fix availability is recorded as `available`, `not available` or `unknown`. A failed, ambiguous or rate limited lookup produces `unknown`, and a policy decision that depends on fix availability is not evaluated as satisfied while the value is `unknown`.
- Pull request dependency results are not SARIF and are not converted to SARIF in order to place them in code scanning. They are reported where the capability natively reports, and that location is stated.
- Where the control depends on a licensed feature, a repository without that licence is reported as outside coverage rather than as clean.

## Implementation

Organization security configuration for the graph and alerting. A centrally maintained reusable workflow for pull request evaluation. A central configuration repository for the update tool.
