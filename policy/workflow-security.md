# Workflow Security

Status: **proposed security requirement**, not adopted, not enforced. Applies in every phase.

Cross-cutting. Applies to every control rather than to one draft.

## Control

Controls that execute in continuous integration cannot be weakened, bypassed or impersonated by the repositories they constrain.

## Policy

- Security controls that require execution are centrally maintained and consumed by reference.
- A calling repository must not be able to alter what the control evaluates or how it decides.
- A workflow must run with the least privilege it needs.
- Untrusted content must not execute in a privileged context.
- Long-lived deployment credentials must be replaced by short-lived federated credentials where the platform supports it.

## Technical requirements

- A caller references a versioned central reusable workflow. Scanner versions, policy content, thresholds, exclusions and evidence schemas are not caller inputs. Caller inputs are limited to an enumerated set that cannot change the outcome. An unrecognised input fails the call natively for a reusable workflow. A central composite action validates its own inputs and fails on an unrecognised one, because the runner only warns.
- Workflows trigger on pull requests and on merge queue events, and report the same check name on both, since a check that is absent on a merge queue event leaves the merge unverified.
- Default workflow token permissions are read-only at organization level, with additional permissions granted per job where required.
- Third-party actions are referenced by full commit SHA, enforced through the organization actions policy, so an unpinned reference fails rather than relying on review. The same policy does not cover reusable workflow references, which remain resolvable by tag. A reusable workflow reference is pinned by full commit SHA and the pin is verified by the central pull request check that validates caller configuration.
- For private and internal repositories, sending secrets and sending write tokens to workflows triggered by fork pull requests are disabled at organization level. These settings do not exist for public repositories, where the platform withholds both by default. For all repositories, approval is required before a fork pull request workflow runs.
- A workflow that reads, renders, builds, scans or executes pull request content does not run in a context that carries write access or secrets. On public repositories this is not settable at organization level, so `pull_request_target` and `workflow_run` combined with a checkout of head ref content are prohibited, and the prohibition is verified by the central caller configuration check.
- Deployment uses federated short-lived credentials rather than stored long-lived credentials where the target platform supports federation. Where it does not, the stored credential is scoped to one environment and rotated on a stated schedule.
- Self-hosted runners are not used for public repositories. Where used, they are ephemeral and scoped to a runner group.
- Scanner and publishing jobs carry explicit timeouts. Run cancellation must not leave a cancelled run readable as a pass.
- Uploaded artifacts and logs contain no credentials, rendered secret values or sensitive configuration. Debug output that would render a resolved secret is not enabled by default.

## Implementation

Organization actions policy and security configuration for the platform controls. Centrally maintained reusable workflows in the designated control repository for the executing controls.
