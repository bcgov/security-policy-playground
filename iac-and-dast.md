# Draft: Infrastructure as Code and Dynamic Testing

Status: **draft** — not adopted, not enforced.

## Control

Deployment manifests are checked against a security baseline before merge, and running non-production environments are probed for what static analysis cannot see.

## Current state

- **IaC** — Trivy repo mode in
  [`analysis.yml#L112-L131`](https://github.com/bcgov/quickstart-openshift/blob/main/.github/workflows/analysis.yml#L112-L131)
  covers config/misconfiguration scanning of the checkout, with an ignore file at `.github/.trivyignore` and a cached DB. This is the surface Security's Checkov action would overlap.
- **DAST** — [`scheduled.yml#L100-L110`](https://github.com/bcgov/quickstart-openshift/blob/main/.github/workflows/scheduled.yml#L100-L110)
  runs `zaproxy/action-full-scan` against the deployed test environment and files findings as an issue titled "ZAP Security Report"
  (example: [quickstart-openshift#2818](https://github.com/bcgov/quickstart-openshift/issues/2818)).
  Note this is the **full** scan, not the baseline scan.

## Gap

- Nobody has compared Trivy's config rules against Checkov's for Kubernetes, Helm and Dockerfiles. Adopting Checkov without that comparison buys duplicate findings in two tools with two ignore mechanisms (`.trivyignore` vs `.checkov.yaml`).
- ZAP findings land in an issue and stay there. There is no triage SLA and no closure criterion, so the report accumulates rather than resolves.

## Proposed policy

- One IaC scanner as the merge gate, not two. Run Checkov and Trivy side by side on the quickstart for one sprint, diff the findings, then pick. Whichever loses may still run advisory, but only one blocks.
- Baseline checks that must be enforced regardless of tool: no privilege escalation, no root execution, resource limits set, read-only root filesystem where the workload allows it.
- DAST stays scheduled against deployed non-production environments. It does not run on pull requests — that needs per-PR preview environments with seeded data and auth state, which we do not have.
- ZAP findings get a triage owner and a severity-based response window. An open ZAP issue with no assignee after one cycle is the escalation trigger.

## Owner

Platform runs the pipelines; Security owns the rule baseline and the ZAP triage window.

## Open questions

- Who runs the Checkov-vs-Trivy diff, and against which repo?
- Are ZAP's CSP and header findings in scope for app teams, or a platform-level ingress concern?
