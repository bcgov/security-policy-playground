# Draft: Supply Chain and Containers

Status: **draft** — not adopted, not enforced.

## Control

Every deployed image can be traced to the workflow, commit and actor that built it, and its contents are enumerated and scanned.

## Current state

[`bcgov/action-builder-ghcr`](https://github.com/bcgov/action-builder-ghcr/blob/main/action.yml) already does most of this:

- **SBOM** — Syft generates CycloneDX and SPDX at
  [L353-L411](https://github.com/bcgov/action-builder-ghcr/blob/main/action.yml#L353-L411), on by default (`sbom` input), uploaded as a build artifact.
- **Provenance attestation** — `actions/attest-build-provenance@v4.2.2` at
  [L414-L430](https://github.com/bcgov/action-builder-ghcr/blob/main/action.yml#L414-L430).

Security's list has both of these as "not yet built, sequenced later". They shipped.

## Gap

Two real ones, both about enforcement rather than capability:

1. **Both steps fail soft.** SBOM generation emits `::warning::` and sets `sbom_files_exist=false` on failure; the build continues. Attestation is skipped with a warning when the calling workflow lacks `id-token: write` and `attestations: write`
   ([L414-L423](https://github.com/bcgov/action-builder-ghcr/blob/main/action.yml#L414-L423)).
   A policy reading "every production image has an SBOM and an attestation" is unenforceable until the caller's permissions are checked at onboarding or the action fails hard.
2. **No image CVE scan.** Trivy in the quickstart runs in *repo* mode against the checkout — it never scans the built image. Container-layer CVEs are currently unscanned. This is the actual missing control in the "already covered" story.

## Proposed policy

- SBOM in both CycloneDX and SPDX for every image pushed to GHCR. Retention matches the image.
- Provenance attestation for every production-bound image, pushed to the registry. Callers must grant `id-token: write` and `attestations: write` — a build without them is a policy failure, not a warning.
- Image scanned after build. `critical` with a fix available blocks deploy; `critical` with no upstream fix needs a logged, dated exception. SARIF to the Security tab under its own category so it does not collide with `trivy` repo-mode results.
- Renovate stays the dependency updater via [`bcgov/renovate-config`](https://github.com/bcgov/renovate-config). No Dependabot PRs alongside it.
- `actions/dependency-review-action` on pull requests for the licence and known-vulnerability gate. This is what Security means by SCA — it is not the same thing as an SBOM, which is an inventory rather than a gate.

## Owner

Platform (builder action and quickstart); Security sets severity thresholds and the exception process.

## Open questions

- Fail the build on missing attestation permissions, or catch it at onboarding?
- Where do image-scan exceptions live — an issue label, or a file in the repo?
