# Security and public-repository safety

This repository is public and is intended to be cloned into environments that may contain non-public company data.

## Never commit

- API keys, access tokens, cookies, credentials, certificates or private keys
- internal hostnames, private repository URLs or confidential ticket/document links
- proprietary PowerPoint templates, brand assets or screenshots
- customer/company source data or generated material derived from it
- prompts, run logs, traces or session state containing confidential content

Use ignored local directories such as `.local/`, `private/` and `artifacts/` for non-public runtime inputs and outputs.

## If a secret is committed

1. **Rotate or revoke it immediately.** Assume it is compromised once pushed to a public repository.
2. Remove the secret from the current tree.
3. Clean Git history when appropriate; deleting it in a later commit does not remove it from previous commits.
4. Check forks, caches, CI logs and generated artifacts that may also contain the value.
5. Review nearby files/logs for related confidential information.

## Reporting a vulnerability

Do not post real credentials, confidential company data or exploit details containing secrets in a public GitHub Issue.

For ordinary bugs that do not expose sensitive information, use GitHub Issues. For sensitive security reports, use GitHub's private vulnerability reporting/security-advisory mechanism if it is enabled for this repository, or contact the repository owner privately.

## Agent/tooling rule

The harness must treat local company material as runtime input, not repository content. Tool logs and telemetry should redact secrets and should default to local/ignored storage unless explicitly configured otherwise.
