# Contributing Guide

Thank you for taking the time to contribute!  
We welcome contributions from everyone who shares our mission to make trust and assurance verifiable, reproducible, and transparent.

This guide will help you get started quickly, follow project conventions, and understand our review and release workflow.

---

## Overview

We value contributions in many forms:

- Code improvements (features, bug fixes, performance)
- Documentation (tutorials, examples, guides)
- Design discussions (ADRs, RFCs)
- Testing and QA
- Community support (issues, discussions, triage)

Whether you’re fixing a typo or designing a new module, your help improves the ecosystem.

---

## Getting Started

1. Fork the repository and clone it locally:

   ```bash
   git clone https://github.com/<your-username>/<repo-name>.git
   cd <repo-name>
   ```

2. Set up the development environment:

   ```bash
   poetry install
   pre-commit install
   ```

3. Run tests to confirm setup:

   ```bash
   make test
   ```

4. Create a feature branch:

   ```bash
   git checkout -b feat/my-awesome-change
   ```

---

## Development Workflow

### 1. Write clean, modular code

Follow existing project style guides and conventions.  
For Python, we recommend:
- Black for formatting
- Ruff for linting
- Mypy for static typing

### 2. Lint, format, and test

Before submitting a PR:

```bash
make lint
make test
```

### 3. Use Conventional Commits

We use Conventional Commits for automated changelogs and semantic versioning.

Examples:
```
feat: add OCI signing verification
fix: correct Tekton pipeline schema validation
docs: update ADR formatting guide
chore: bump dependencies
```

### 4. Sign your commits (DCO)

All commits must be signed off to certify origin under the Developer Certificate of Origin (DCO):

```bash
git commit -s -m "feat: add privacy manifest schema"
```

If you forget, you can sign retroactively:

```bash
git commit --amend -s
git push --force
```

---

## Pull Requests

1. Ensure your branch is up to date with main:

   ```bash
   git fetch origin main
   git rebase origin/main
   ```

2. Run all tests and lint checks.

3. Open a Pull Request:
   - Clearly describe what and why.
   - Reference related issues (e.g., Fixes #123).
   - Fill in the PR checklist template.

4. Wait for at least one maintainer approval.

5. CI checks must pass before merge.

---

## Testing

Tests live under the tests/ directory.  
We expect unit + integration coverage ≥ 80%.

Run tests locally:

```bash
pytest -v
```

Generate a coverage report:

```bash
pytest --cov=src
```

---

## Documentation

Docs live in the docs/ folder and are built using Material for MkDocs and Awesome Pages.

To preview locally:

```bash
mkdocs serve
```

When contributing documentation:
- Use clear headers (## preferred for sections)
- Use callouts for emphasis (!!! note, !!! tip, ??? info)
- Reference related ADRs or RFCs where relevant
- Keep diagrams in docs/diagrams/ and reference them with relative paths

---

## Security

Please do not report security vulnerabilities via public issues.

Instead, see SECURITY.md for our disclosure policy and private reporting process.

---

## Governance

Project direction, roadmap, and major design decisions are captured in:

- Design Decision Records (ADRs)
- Governance.md
- Code of Conduct

---

## Community Conduct

We adhere to the Contributor Covenant Code of Conduct.  
Be respectful, inclusive, and constructive in all interactions.

---

## Quick Summary

| Step | Command / Action |
|------|------------------|
| 1 | `git clone ...` |
| 2 | `poetry install` / `make setup` |
| 3 | `git checkout -b feat/xyz` |
| 4 | `git commit -s -m "feat: add xyz"` |
| 5 | `make lint && make test` |
| 6 | `git push origin feat/xyz` |

---

## Thanks

Your time and expertise make this project stronger.  
We’re excited to collaborate with you on building trustworthy, verifiable systems.

"Transparency builds trust. Reproducibility keeps it."
