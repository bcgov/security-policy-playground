# Infrastructure as Code

Status: **proposed security requirement**, not adopted, not enforced. Evaluation phase.

Requirements for the control drafted in [iac-and-dast.md](../iac-and-dast.md). That draft records the current state and the gap; this document states the requirement.

## Control

Infrastructure and deployment definitions are evaluated for misconfiguration before merge, in the form that is applied to the target platform.

## Policy

- One scanner holds this capability at a time. A second scanner may run during a time boxed comparison against the same corpus, and is removed at the end of it. A permanent second scanner producing substantially the same findings must not be introduced without a coverage gap measured on that corpus and adjudicated by Security.
- Scope is every infrastructure format the approved scanner supports that the repository actually uses.
- A definition that wraps resources in a structure the scanner does not descend into must be rendered before it is evaluated. A result of zero findings must not be produced by a scan that evaluated zero resources.
- The baseline must not contain a rule the platform contradicts.
- Findings are advisory during the evaluation phase. What is never advisory is listed in [scan-integrity](scan-integrity.md). Rendering failure, scanner failure and unexpected zero coverage are not advisory.

## Technical requirements

- Rendering for scan purposes uses placeholder or default parameter values. It must not use deployment parameters and must not use platform credentials, because a rendered definition can contain resolved secret values.
- Before rendered output is scanned, uploaded or retained, secret values are replaced with fixed placeholders while object structure is preserved, so misconfigurations in those objects are still evaluated. A value that cannot be sanitised deterministically is excluded and the exclusion is recorded. Raw rendered output must not be published.
- Each run records the targets discovered, the targets evaluated, the exclusions applied, and the scanner and policy versions. A run where relevant targets are present and none were evaluated fails the check.
- The policy baseline evaluates privilege escalation and privileged execution, execution as root, security context, host namespace access, writable root filesystems, retained Linux capabilities, unnecessary service account token mounting, network segmentation in both ingress and egress directions, wildcard and unconstrained identity permissions, secrets supplied through unsafe configuration, mutable image references, unpinned modules and providers, encryption at rest and in transit, and audit logging where the resource supports it.
- Memory limits are part of the baseline for application containers. CPU limits are excluded where the hosting platform's guidance directs that containers burst into unallocated node capacity, and the exclusion is held as a platform-driven exception rather than as an absent rule. Where a platform allocates user and group identifiers at admission time, hardcoding them is prohibited rather than required, since a hardcoded identifier causes the workload to be rejected.
- Results are published under a category specific to infrastructure misconfiguration, carrying misconfiguration findings only. Where the same scanner also performs vulnerability or secret analysis, those results are published separately.

## Implementation

A centrally maintained reusable workflow, with the baseline and its exceptions held centrally rather than per repository, under [exceptions](exceptions.md).
