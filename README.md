# security-policy-playground

Draft ideas for security policies.  Please don't take them too seriously!

Somewhere to argue about security controls before anything becomes a rule. Nothing here is adopted or enforced.

## How these are written

Each draft states one control, then what actually exists today with a link to the line that proves it, then the gap. Claims about existing tooling are cited to
[`bcgov/quickstart-openshift`](https://github.com/bcgov/quickstart-openshift) and
[`bcgov/action-builder-ghcr`](https://github.com/bcgov/action-builder-ghcr) so a disagreement is about the policy, not about what we run.

Preference throughout: a GitHub Enterprise org setting beats a composite action we have to maintain in every repo.

| Draft | Control |
| :--- | :--- |
| [secrets-and-credentials](policies/secrets-and-credentials.md) | Secret scanning, push protection, rotation |
| [sast-and-code-quality](policies/sast-and-code-quality.md) | CodeQL, SonarCloud |
| [supply-chain-and-containers](policies/supply-chain-and-containers.md) | SBOM, attestations, image CVEs, dependency review |
| [iac-and-dast](policies/iac-and-dast.md) | Checkov/Trivy manifest scanning, OWASP ZAP |

## Three corrections worth reading first

Comparing the security team's list against what is running:

- **Attestations and SBOM are already shipped**, not "sequenced later" — both are in `action-builder-ghcr`. The open question is enforcement, since both steps fail soft.
- **Container images are not scanned.** Trivy in the quickstart runs in repo mode against the checkout, not against the built image. This gets described as container scanning and it is not.
- **There is no CodeQL.** The SARIF upload people cite is Trivy's, using `github/codeql-action/upload-sarif`. Since there is nothing to migrate, org-wide Default Setup is the cheap path.

## Contributing

Open a PR against the relevant draft. Correct a factual claim by replacing the link, not by softening the wording. Enforcement-mechanism arguments (org ruleset vs. pipeline step) belong in issues.
