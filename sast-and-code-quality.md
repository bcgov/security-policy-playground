# Draft: Static Analysis and Code Quality

Status: **draft** — not adopted, not enforced.

## Control

Source code is analysed for vulnerabilities and quality regressions on every pull request, before merge to a protected branch.

## Current state

- SonarCloud runs on backend and frontend in
  [`quickstart-openshift/.github/workflows/analysis.yml`](https://github.com/bcgov/quickstart-openshift/blob/main/.github/workflows/analysis.yml#L39-L84),
  with per-component project keys under the `bcgov-sonarcloud` organization.
- Trivy runs in repo mode in the same workflow
  ([L112-L131](https://github.com/bcgov/quickstart-openshift/blob/main/.github/workflows/analysis.yml#L112-L131))
  and uploads SARIF to the Security tab under category `trivy`.

## Gap

**There is no CodeQL in the quickstart template.** The workflow directory contains `analysis`, `merge`, `pr-close`, `pr-open`, `pr-validate`, `reusable-deploy`, `reusable-tests` and `scheduled` — no `codeql.yml`, and no CodeQL step inside any of them. The SARIF upload people point to as "our CodeQL" is Trivy's, using the `github/codeql-action/upload-sarif` action. That action name is the only CodeQL in the repo.

This makes the case for CodeQL **Default Setup** at org level rather than a composite action: there is no existing per-repo CodeQL config to migrate, so the cheapest path is also the correct one.

## Proposed policy

- CodeQL enabled via org-level Default Setup for every repo with a supported language. No `bcgov/actions` CodeQL wrapper.
- Advanced Setup (`.github/workflows/codeql.yml`) only where a build needs specific compile flags, justified in the PR that adds it.
- `high` and `critical` CodeQL findings block merge to `main`.
- SonarCloud quality gate stays a required check where it is already wired. Onboarding friction is a docs problem, not a policy problem.

## Owner

Platform enables Default Setup; Security sets the severity threshold.

## Open questions

- Does Default Setup handle the repos with unusual build steps, or do we need a short Advanced Setup list up front?
- Do CodeQL and SonarCloud findings get triaged in one place or two?
