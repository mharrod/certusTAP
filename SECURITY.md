# Security Policy

Thank you for helping us keep this project secure and trustworthy.

This document outlines our security disclosure process and expectations for responsible reporting.

---

## Supported Versions

We release patches and updates for the latest stable version of the project.  
Older versions may not receive security updates unless explicitly stated.

| Version | Supported |
|----------|------------|
| Latest (`main`) | ✅ |
| Previous stable release | ⚠️ (critical fixes only) |
| Older releases | ❌ |

---

## Reporting a Vulnerability

If you believe you’ve found a security vulnerability in this project, please **do not open a public issue**.  
Instead, report it privately via one of the following methods:

- **Email:** certustap@gmail.com  
- **GitHub:** Use the "Report a vulnerability" feature under the Security tab of this repository.

Please include as much detail as possible so we can reproduce and investigate the issue:
- Steps to reproduce
- Affected component(s) or configuration(s)
- Possible impact
- Suggested mitigation (if any)

We will acknowledge receipt of your report within **48 hours** and provide a status update within **5 business days**.

---

## Coordinated Disclosure

We follow a **coordinated disclosure policy** to responsibly address and communicate vulnerabilities.

1. You report a vulnerability through a private channel.  
2. We confirm and triage the issue internally.  
3. A fix is developed, reviewed, and merged.  
4. We coordinate a release with a public advisory (CVE if applicable).  
5. You are credited in the disclosure unless you request anonymity.

---

## Security Best Practices for Contributors

If you contribute code to this repository, please follow these guidelines:

- Do not include secrets, keys, or credentials in commits.  
- Avoid using unpinned dependencies or outdated libraries.  
- Use secure protocols (HTTPS, SSH).  
- Run `make lint` and `make test` before submitting a PR.  
- Review dependencies with `poetry export` or `pip-audit`.

---

## Security Contact

For all urgent or sensitive issues, please reach out to the maintainers via:

**certustap@gmail.com**

Thank you for helping us build secure, verifiable, and trustworthy systems.
