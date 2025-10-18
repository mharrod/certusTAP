# Testing 

Below is the proposed testing plan for the PoC. Not all testing will be done on day one, but most of the testing categories should be in use before the end of the "Fly Phase".

---

## Foundational Baselines  

:material-bullseye-arrow: **Goal:** Establish reproducibility and consistent environments for all assurance workflows.  

| **Category** | **Details** |
| ------------- | ------------ |
| **Approach** | - Validate environment reproducibility (`make test-env`) <br> - Snapshot tool versions (`bandit --version`, `trivy --version`, etc.) <br> - Run dependency hygiene checks: `poetry check`, `pip-audit` <br> - Verify documentation build integrity: `mkdocs build --strict` |
| **Expected Output** | - `baseline.json` and `baseline.hash` signed with Cosign <br> - Verification logs confirming environment consistency <br> - Dependency and documentation validation results |

---

## Static & Functional Testing  

:material-bullseye-arrow: **Goal:** Ensure correctness, code safety, and maintainability at rest.  

| **Category** | **Details** |
| ------------- | ------------ |
| **Scope** | - Core Python modules (`ingest`, `retrieve`, `generate`, etc.) <br> - Security and code quality scans (SAST + linting + typing) |
| **Approach** | - Run unit tests with `pytest` (coverage > 85%) <br> - Lint and format: `ruff`, `flake8`, `black` <br> - Type checks: `mypy` <br> - Code complexity: `radon cc -s src/` <br> - Detect unused code: `vulture` <br> - SAST: `bandit -r src/` <br> - Secrets: `gitleaks detect` <br> - Dependency CVEs: `trivy fs .` or `grype` |
| **Expected Output** | - Coverage and quality reports (JSON) <br> - Linting and complexity metrics <br> - Signed SARIF outputs <br> - OCI artifact: `oci://org/cap/static:<commit-hash>` |

---

## Dynamic & Runtime Security Testing  

:material-bullseye-arrow: **Goal:** Identify exploitable vulnerabilities in live or running environments.  

| **Category** | **Details** |
| ------------- | ------------ |
| **Scope** | - FastAPI endpoints, dashboards, webhooks <br> - Authentication, rate limiting, input validation |
| **Approach** | - Deploy test environment via Dagger or Tekton <br> - DAST with OWASP ZAP <br> - Nuclei scans (`cves`, `exposures`, `misconfigurations`) <br> - Optional Burp Suite CI or fuzzers <br> - Performance regression: `pytest-benchmark` or `locust` |
| **Expected Output** | - DAST and Nuclei JSON/SARIF reports <br> - Runtime performance baselines <br> - OCI artifact: `oci://org/cap/dynamic:<commit-hash>` |

---

## Infrastructure & IaC Security Testing  

:material-bullseye-arrow: **Goal:** Validate security and compliance of infrastructure-as-code and container configurations.  

| **Category** | **Details** |
| ------------- | ------------ |
| **Scope** | - Terraform, Kubernetes manifests, Dockerfiles, GitHub Actions |
| **Approach** | - Policy checks: `checkov -d infra/`, `tfsec` <br> - Image scanning: `trivy image myimage:latest` <br> - Config scanning: `trivy config .` <br> - Dockerfile linting: `hadolint Dockerfile` <br> - IaC validation: `terraform validate`, `kubectl apply --dry-run` <br> - Apply CIS or custom Assurance policies |
| **Expected Output** | - IaC and container reports (SARIF + JSON) <br> - Signed attestation: `oci://org/cap/infra:<commit-hash>` |

---

## Integration & End-to-End (E2E) Testing  

:material-bullseye-arrow: **Goal:** Validate the entire assurance workflow from ingestion to reporting.  

| **Category** | **Details** |
| ------------- | ------------ |
| **Approach** | - Use golden repos with known vulnerabilities <br> - Execute `scan run --repo <fixture>` <br> - Compare deterministic outputs (`report.json`, `report.md`) <br> - Validate schema (Pydantic) and API contracts (Schemathesis) <br> - Verify signatures (Cosign) and reproducibility |
| **Expected Output** | - Signed E2E attestation: `oci://org/cap/e2e:<commit-hash>` <br> - Deterministic test outputs with verified signatures |

---

## Assurance Integrity & Non-Repudiation  

:material-bullseye-arrow: **Goal:** Verify the trustworthiness and provenance of all assurance artifacts.  

| **Category** | **Details** |
| ------------- | ------------ |
| **Approach** | - Verify Cosign signatures <br> - Rebuild provenance with In-Toto links <br> - Validate Rekor transparency logs <br> - Detect expired/invalid signatures <br> - Documentation audit: `interrogate` <br> - Remove stale code: `vulture` |
| **Expected Output** | - Provenance verification logs <br> - Signed verification evidence <br> - Documentation coverage report |

---

## AI Assurance & Evaluation  

:material-bullseye-arrow: **Goal:** Validate reasoning reliability, explainability, and fairness in AI-assisted components.  

| **Category** | **Details** |
| ------------- | ------------ |
| **Approach** | - Evaluate EXPLAIN/FIX/SUMMARIZE chains (DeepEval) <br> - Faithfulness ≥ 0.9, Relevancy ≥ 0.85, Consistency ≥ 0.8, Safety = 0 <br> - Schema and safety validation: GuardrailsAI / Promptfoo <br> - Data consistency: Pandera / Great Expectations <br> - Feature attribution: SHAP, LIME <br> - Sign prompt provenance |
| **Expected Output** | - DeepEval and Guardrails reports <br> - Drift and reasoning deltas <br> - OCI artifact: `oci://org/cap/ai-eval:<commit-hash>` |

---

## Continuous Regression & Drift Detection  

:material-bullseye-arrow: **Goal:** Detect drift in security posture, assurance logic, and AI reasoning quality.  

| **Category** | **Details** |
| ------------- | ------------ |
| **Approach** | - Scheduled assurance jobs <br> - Compare DeepEval metrics, SBOM diffs, manifests <br> - Mutation testing (`mutmut run`) <br> - Trigger re-evaluation if thresholds exceeded |
| **Expected Output** | - Drift reports (`drift.json`) <br> - Mutation testing score (≥80%) <br> - OCI artifact: `oci://org/cap/drift:<commit-hash>` |

---

## Human-in-the-Loop & Workflow Validation  

:material-bullseye-arrow: **Goal:** Validate manual approvals, waivers, and review workflows.  

| **Category** | **Details** |
| ------------- | ------------ |
| **Approach** | - Simulate analyst and approver interactions <br> - Validate RBAC and countersignatures <br> - Attempt unauthorized modifications <br> - Accessibility checks: `axe-core`, `lighthouse-ci` <br> - Verify signed waiver artifacts |
| **Expected Output** | - Workflow validation log <br> - Accessibility compliance report <br> - OCI artifact: `oci://org/cap/hitl:<commit-hash>` |

---

## Chaos & Resilience Testing  

:material-bullseye-arrow: **Goal:** Validate the reliability and resilience of the assurance pipeline.  

| **Category** | **Details** |
| ------------- | ------------ |
| **Scope** | - Registry downtime, corrupted artifacts, key rotation, interrupted pipelines |
| **Approach** | - Simulate failure and recovery <br> - Verify resumption and signature integrity <br> - Measure recovery time and stability |
| **Expected Output** | - Recovery and degradation reports <br> - Verified integrity logs <br> - OCI artifact: `oci://org/cap/chaos:<commit-hash>` |

---

## Comprehensive Quality Audit & Attestation  

:material-bullseye-arrow: **Goal:** Produce a final aggregated quality and assurance attestation covering all phases.  

| **Category** | **Details** |
| ------------- | ------------ |
| **Approach** | - Aggregate results from all phases <br> - Consolidate performance, complexity, documentation <br> - Generate unified Quality Attestation (JSON + Markdown) <br> - Sign and store in OCI registry |
| **Expected Output** | - Unified quality summary <br> - Signed attestation: `oci://org/cap/quality:<commit-hash>` |


---

## Unified Metrics and Acceptance Criteria

| Metric | Tool/Source | Target | Description |
| ------- | ------------ | ------- | ------------ |
| Test Coverage | Pytest | ≥85% | Unit test completeness |
| Linting & Type Checks | Ruff, Mypy | 100% | Static code correctness |
| Complexity Threshold | Radon | ≤10 | Maintainable code |
| Mutation Score | Mutmut | ≥80% | Test robustness |
| Documentation Coverage | Interrogate | ≥80% | Code clarity |
| Performance Regression | Pytest-benchmark | <20% diff | Runtime stability |
| AI Faithfulness | DeepEval | ≥0.9 | Reasoning reliability |
| Drift Detection Latency | Pipeline logs | <24h | Responsiveness |
| Reviewer SLA | Workflow logs | ≤48h | Human validation timeliness |

---

## Governance and Reporting

- All phases emit signed evidence to `oci://org/cap/tests/<run-id>`.
- CI/CD pipelines push metrics to Prometheus/Grafana dashboards.
- Failed thresholds create automated Jira issues (P1–P4).
- Monthly reports include:
  - DeepEval reasoning metrics
  - Drift deltas
  - Mutation and coverage summaries
  - Signature freshness status

---

## Continuous Improvement and Future Enhancements

- Expand Fuzz Testing (code, prompts, manifests).
- Integrate runtime threat detection (Falco, eBPF).
- Develop adversarial LLM red-team tests.
- Add formal verification for Policy DSLs.
- Establish inter-org attestation replays for federated trust validation.

---
