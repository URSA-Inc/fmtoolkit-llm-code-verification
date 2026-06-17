# Contributing

Thanks for your interest in `fmtoolkit-llm-code-verification`. This repository is
the open client and integration tooling that connects an open source project's
code to the FMToolkit verification service. Contributions that make it easier for
more projects to triage AI-generated commits are very welcome.

## Scope

This repo contains the client, CI integrations, and reporting. It does **not**
contain the verifier itself (the FMToolkit service) and does not generate code.
Useful areas to contribute:

- CI integrations for additional forges (GitLab, Gitea, Forgejo, …)
- Reporting formats and PR-comment templates
- Configuration for project-specific merge policies (e.g. how to treat
  high-confidence vs. fully proven verdicts)
- Documentation, examples, and onboarding guides for maintainers

If you're planning a larger change, please open an issue first so we can agree on
the approach before you invest the time.

## How to contribute

1. Fork the repository and create a branch from `main`.
2. Make your change, with tests where it makes sense.
3. Run the linters and test suite locally.
4. Open a pull request describing what changed and why. Link any related issue.

## Development setup

```bash
git clone https://github.com/<your-fork>/fmtoolkit-llm-code-verification.git
cd fmtoolkit-llm-code-verification
pip install -e ".[dev]"
pytest
```

You do not need an LLM API key to work on this project. To run integration tests
against the verifier you will need an FMToolkit access token; most changes can be
developed and tested against the recorded fixtures without one.

## Licensing of contributions

This project is licensed under the Apache License 2.0. By submitting a
contribution, you agree that your contribution is licensed under the same terms
(inbound = outbound), and you confirm you have the right to submit it. Please do
not include code, models, prompts, or data you are not permitted to contribute.

## Code of conduct

Be respectful and constructive. Maintainers may remove comments, commits, or
contributions that are not aligned with a welcoming, professional environment.

## Reporting security issues

Please do not file public issues for security vulnerabilities. Email
security@ursasecure.com with details, and we will coordinate a fix and disclosure.
