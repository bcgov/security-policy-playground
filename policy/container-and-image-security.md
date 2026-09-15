# Container and Image Security

Status: **proposed security requirement**, not adopted, not enforced. Evaluation phase.

Requirements for the control drafted in [supply-chain-and-containers.md](../supply-chain-and-containers.md). That draft records the current state and the gap; this document states the requirement.

## Control

Every deployed image can be traced to the workflow, commit and actor that produced it, and its contents are enumerated and scanned.

## Policy

- Vulnerability scanning must evaluate the built image, identified by digest, after build and before promotion.
- Inventory and provenance must be bound to the same digest as the image they describe.
- A build that cannot produce required evidence must report a failure. Missing evidence must not be reduced to a warning.
- One authoritative vulnerability result per scope. A second source must not be maintained for the same scope without a defined precedence.
- The blocking threshold is critical with a fix available. A critical finding with no upstream fix is recorded and dated under [exceptions](exceptions.md) rather than discarded.

## Technical requirements

- The scan covers operating system packages and bundled application dependencies of the final image. Filesystem scanning of the repository does not satisfy this requirement and must not be described as image scanning.
- Inventory is generated at build time, in CycloneDX and SPDX, associated with the image digest, and retained for as long as the image is deployable. A workflow artifact under default retention does not satisfy retention on its own.
- Provenance attestation is produced for the same digest.
- Deployment resolves the image by digest, and the attestation for that digest is verified before rollout.
- Tools that generate required evidence are installed from a pinned version with a verified checksum or signature, whether they arrive through an action or through a script. An evidence-producing step must not install its tooling from a moving branch.
- Actions and reusable workflows that produce evidence are referenced by full commit SHA, so the evidence identifies the version that produced it. For actions this is enforced by the organization actions policy. For reusable workflows it is verified by the central caller configuration check, because the actions policy does not cover them.
- A step that produces required evidence must not suppress its own failure.
- Image results are published under a category distinct from repository scanning results.
- A result from a deployed workload monitoring platform is a second source. It does not substitute for pre-promotion scanning, whatever digest or policy version it identifies. Where the two disagree, the pre-promotion result takes precedence.

## Implementation

Centrally maintained build and deployment actions.
