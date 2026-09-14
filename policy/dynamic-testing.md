# Dynamic Testing

Status: **proposed security requirement**, not adopted, not enforced. Evaluation phase.

Requirements for the control drafted in [iac-and-dast.md](../iac-and-dast.md). That draft records the current state and the gap; this document states the requirement.

## Control

Running non-production environments are probed for behaviour static analysis cannot observe.

## Policy

- Scanning runs on a schedule against approved non-production targets.
- A production target requires explicit prior authorization.
- A target must not be supplied by an untrusted caller.
- Findings must have a named triage owner, a response window by severity, and a closure criterion, so a report resolves rather than accumulates.

## Technical requirements

- The target is derived by the central workflow from the calling repository's identity and the approved environment naming scheme. It is not a caller input. Where a repository's environment address does not follow the scheme, the address is registered centrally against that repository and the workflow resolves it from the register. An address registered to another repository is rejected. A domain allowlist does not satisfy this requirement, because every non-production environment in the estate sits under one wildcard and an allowlist at that granularity authorises scanning another team's environment.
- Active scanning is distinguished from passive scanning in the requirement and in the run record. An active scan against a shared platform runs only against targets whose owner has authorized active traffic, on the published schedule.
- Coverage includes the application's published interface definition where the application publishes one. Crawling the user interface alone leaves endpoints that no page links to unreached.
- Native tool output is retained as the authoritative result. SARIF is not required from a tool that does not emit it accurately, and the reporting location is stated instead.
- Authenticated scanning and interface-driven scanning are introduced only with an approved design for credential handling and test data.
- The scan has an explicit timeout. A timeout or a failure to reach the target is reported as a failed control rather than as a clean result.
- Findings are recorded on the surface named in [security-results](security-results.md), with the owner and response window recorded alongside.

## Implementation

A centrally maintained reusable workflow, running on a schedule against deployed non-production environments.
