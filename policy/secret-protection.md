# Secret Protection

Status: **proposed security requirement**, not adopted, not enforced. Applies in every phase. This control blocks at push time.

Requirements for the control drafted in [secrets-and-credentials.md](../secrets-and-credentials.md). That draft records the current state and the gap; this document states the requirement.

## Control

No credential, token, private key or connection string reaches a repository, and any that does is treated as compromised.

## Policy

- Secret scanning and push protection must be enabled for every repository in scope, public and private, and must not be disableable by a repository administrator.
- A push containing a detected secret must be refused at push time, not reported after the fact.
- A bypass is permitted only for a value that is provably not a live credential, such as a test fixture, a documentation example or a key that has been rotated and revoked. It must be attributable, justified at the time it is taken, and reviewable.
- A committed credential must be rotated or revoked at the provider. History rewriting is optional cleanup and must not be treated as the remediation.
- A detection pattern must not be promoted to blocking before its match behaviour is known.
- A match that is not a credential must be handled as a recorded exception under [exceptions](exceptions.md), not by disabling the pattern for everyone.

## Technical requirements

- Enablement is applied through an organization security configuration set to enforce, so the settings cannot be changed at repository level. A setup script run per repository does not satisfy this requirement.
- Push protection is enabled for all supported provider patterns.
- For organization-owned private and internal repositories with GitHub Secret Protection, bypass is routed through delegated bypass, so a contributor without bypass rights raises a request that a named reviewer group approves or denies. A request that is neither approved nor denied expires after seven days. Public repositories cannot use delegated bypass. For those the recorded bypass reason plus scheduled audit log review is the control, not an interim measure, and the limitation is stated in the coverage record.
- Bypass and dismissal events are reviewed monthly by Security together with one Platform representative. The audit log has a bounded retention window, so these events are streamed to an evidence store with a retention period of two years.
- Custom patterns require GitHub Secret Protection and are unavailable on public repositories. Where they are available, a new pattern is validated in dry run at organization level, which reports a sample of matches without raising alerts, and is promoted to blocking individually rather than as a set. Where the sample is saturated the pattern is narrowed and revalidated before promotion.
- Secret detection implemented as a workflow step does not satisfy this control, because a workflow runs after the push it would need to prevent.
- Where the control depends on a licensed feature, a repository without that licence is reported as outside coverage rather than as clean.

## Implementation

Organization security configuration with enforcement, and GitHub-native delegated bypass. No reusable workflow.
