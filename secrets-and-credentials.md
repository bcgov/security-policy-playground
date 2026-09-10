# Draft: Secrets and Credentials

Status: **draft** — not adopted, not enforced.

## Control

No credential, token, private key or connection string reaches a repository, and any that does is treated as compromised.

## Current state

Nothing in this control is implemented in a workflow we own, by design. Secret scanning and push protection are enterprise-level switches; a composite action cannot block a `git push`, only report after the fact.

## Gap

- Push protection is not confirmed enabled org-wide for `bcgov`.
- There is no defined bypass review. GitHub logs bypasses with the author's stated reason, but nobody currently reads that log.

## Proposed policy

- Push protection enabled for every repository in the org, public and private, with no per-repo opt-out.
- A bypass is allowed only for a value that is provably not a live credential (test fixture, documentation example, rotated-and-dead key). The reason goes in GitHub's prompt at bypass time.
- Bypass log reviewed monthly by Security plus one Platform representative.
- A committed secret is rotated at the provider first. History rewriting is optional cleanup, never the remediation — assume it was cloned.

## Owner

Security / GitHub Enterprise admins. Platform's only task is including the repo-level defaults in onboarding automation for new
[`bcgov/quickstart-openshift`](https://github.com/bcgov/quickstart-openshift) spin-ups.

## Open questions

- Who holds the bypass-review calendar item?
- Do we alert on bypass in real time, or accept a monthly review window?
