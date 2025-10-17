# Roadmap

 This roadmap outlines the evolution of the Certus TAP PoC from foundational R&D to a fully autonomous, trust-centric ecosystem. Each phase strengthens the integration of AI reasoning, immutable evidence, and human oversight.  We are aiming to ensure that assurance processes remain transparent, verifiable, and explainable. By progressing through a structured Crawl → Walk → Run → Fly approach, we hope to transform early experimental prototypes into production-grade, federated systems capable of sustaining continuous cross-organizational trust validation.

Here is a summary of each phase:

??? info "Crawl — Exploration & Foundational R&D"

    | **Category**               | **Focus**                                                                                                                                           | **Outcomes**                                                                |
    | -------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------- |
    | **Core Platform**          | Establish reproducible local development using Python, Poetry, and Docker. Setup LocalStack(s3) + Harbor registries. Define assurance manifest schemas. | Foundational CLI scripts, developer reproducibility, and schema prototypes. |
    | **Orchestration Pipeline** | Prototype Tekton-based workflows for signed evidence capture. Integrate initial scanner outputs (Trivy, Presidio).                                  | First end-to-end PoC for evidence registration and signature verification.  |
    | **AI Reasoning Rail**      | Minimal; exploratory use of Haystack + LLM for privacy text analysis and metadata tagging.                                                       | Early experiments in reasoning trace capture for explainability.            |
    | **Trust Centre**           | Introduce Sigstore, RFC 3161 timestamp proofs, and Rekor transparency log.                                                                          | Immutable provenance for early evidence artifacts.                          |
    | **Other**                  | Documentation and design decision logs; initial architecture diagrams.                                                                              | Shared R&D record for structured next-phase development.                    |


---

??? info "**Walk — Structured Development & CLI-Driven Workflows**"

    | **Category**               | **Focus**                                                                                                          | **Outcomes**                                                       |
    | -------------------------- | ------------------------------------------------------------------------------------------------------------------ | ------------------------------------------------------------------ |
    | **Core Platform**          | Harden development stack (FastAPI services, Tekton deployment templates, VS Code devcontainers).                   | Reproducible local/test environments and schema validation via CI. |
    | **Orchestration Pipeline** | Implement Tekton pipelines with Cosign signing, RFC 3161 timestamps, and consistent artifact schemas (SARIF/JSON). | Reliable CLI tools and containerized pipeline execution.           |
    | **AI Reasoning Rail**      | Integrate reasoning outputs into normalization pipeline; start tracking explainability metrics via DeepEval.       | Structured reasoning traces linked to artifacts.                   |
    | **Trust Centre**           | Deploy Harbor WORM registry and enforce signature verification on upload.                                          | Authenticated, timestamped artifacts with provenance.              |
    | **Other**                  | Early dashboard for evidence status; internal SME alpha feedback loops.                                            | Alpha validation of usability and reproducibility.                 |


---


??? info "**Run — Defined Scope & Production-Ready Capabilities**"


    | **Category**               | **Focus**                                                                                                          | **Outcomes**                                                          |
    | -------------------------- | ------------------------------------------------------------------------------------------------------------------ | --------------------------------------------------------------------- |
    | **Core Platform**          | Harden container images, policies, and security posture; integrate observability (Prometheus/Grafana).             | Stable, secure deployment baseline for SMEs and auditors.             |
    | **Orchestration Pipeline** | Expand Tekton pipelines for full assurance loops: scanning → normalization → enrichment → gating → signing.        | End-to-end traceability across all pipeline steps.                    |
    | **AI Reasoning Rail**      | Operate reasoning components continuously: contextual analysis, bias/fairness checks, narrative generation.        | Explainable reasoning reports embedded in assurance outputs.          |
    | **Trust Centre**           | Enable governance enforcement (signature chains, audit logs, waivers). Integrate Rekor + Cosign verification APIs. | Production-grade evidence integrity verification and audit readiness. |
    | **Other**                  | Dashboards visualizing compliance, drift, and reasoning quality.                                                   | Live reporting and trace validation for auditors and SMEs.            |

---

??? info "**Fly — Autonomous, Trust-Centric Ecosystem**"
   
    | **Category**               | **Focus**                                                                                                        | **Outcomes**                                                               |
    | -------------------------- | ---------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------- |
    | **Core Platform**          | Deliver multi-tenant architecture; enable federated deployments; optimize performance for large-scale org use.   | Scalable, resilient “Assurance as a Service” platform.                     |
    | **Orchestration Pipeline** | Introduce self-healing pipelines with AI-driven scheduling and dynamic resource allocation.                      | Autonomous orchestration with adaptive policy control.                     |
    | **AI Reasoning Rail**      | Continuous, autonomous AI reasoning with human oversight; AI validation of reasoning reliability and bias drift. | Trustworthy, co-signed AI reasoning evidence with transparency dashboards. |
    | **Trust Centre**           | Federated attestation exchange, cross-org signing, and provenance APIs.                                          | Global trust fabric with verifiable, replayable attestations.              |
    | **Other**                  | Unified UI for policy authors, transparency reports, and governance analytics.                                   | Human–AI collaboration with complete evidence traceability.                |


---

## **Crawl-to-Walk (next 6-months)**

The project is currently in **Crawl** and moving towards **Walk**, we are focusing on:

- Open-ended R&D and architectural experimentation  
- Iterative prototyping with reproducibility and immutability foundations  
- Designing schemas, manifests, and reasoning validation frameworks  
- Preparing the transition to **Walk** with CLI and CI/CD integration groundwork


### 🔵  **A — Foundation & Evidence Layer** 


:material-bullseye-arrow: **Goal:** Establish reproducible environments, baseline security hygiene, and immutable storage foundations.

| **Category**               | **Focus / Deliverables**                                                                                  | **Success Criteria**                                                                  |
| -------------------------- | --------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------- |
| **Core Platform**          | Local reproducible setup with Python + Poetry; LocalStack + Harbor registries; baseline Tekton pipelines. | All developers can spin up identical local environments via devcontainer or Makefile. |
| **Orchestration Pipeline** | Simple Dagger → Tekton prototype for signed artifact flow; reproducible build/test stages.                | Tekton pipeline runs end-to-end locally, generating signed artifacts.                 |
| **AI Reasoning Rail**      | None initially — focus on reproducibility for future reasoning traceability.                              | Reasoning interface stubbed and schema placeholder defined.                           |
| **Trust Centre**           | Introduce Cosign signing, RFC 3161 timestamps, Rekor integration for immutable attestations.              | Cosign + Rekor verification successful on all sample evidence.                        |
| **Other**                  | Architecture documentation and assurance manifest schema version-controlled.                              | Schema and CLI prototype merged and reviewed in repo.                                 |


---

### 🟡 **B — Normalization & Schema Standardization**

:material-bullseye-arrow: **Goal:** Build the unified schema for assurance data across scanners, enrichers, and policy gates.

| **Category**               | **Focus / Deliverables**                                                             | **Success Criteria**                                                |
| -------------------------- | ------------------------------------------------------------------------------------ | ------------------------------------------------------------------- |
| **Core Platform**          | Schema registry for scanner outputs and evidence formats.                            | Schema registry accessible and versioned in repository.             |
| **Orchestration Pipeline** | Normalization engine emitting SARIF + JSON with fingerprinting and severity mapping. | All scanners emit normalized SARIF or JSON with valid fingerprints. |
| **AI Reasoning Rail**      | Introduce structured prompt schema for future reasoning steps.                       | Prompt schema validated via JSON Schema tests.                      |
| **Trust Centre**           | Signed schema manifests stored in Harbor with provenance metadata.                   | Signed manifests visible and retrievable via digest.                |
| **Other**                  | Map scanner outputs to explainable assurance categories.                             | 100% of outputs mapped to unified severity scale.                   |

---

### 🟡 **C — Contextual Enrichment & Threat Intelligence**

:material-bullseye-arrow: **Goal:** Enrich normalized findings with contextual metadata and threat intelligence feeds.

| **Category**               | **Focus / Deliverables**                                                    | **Success Criteria**                                     |
| -------------------------- | --------------------------------------------------------------------------- | -------------------------------------------------------- |
| **Core Platform**          | Enrichment microservice for service, environment, and component metadata.   | Deployed enrichment service producing contextual labels. |
| **Orchestration Pipeline** | Integrate EPSS, KEV, CVSS, and CWE threat feeds into enrichment workflow.   | Nightly jobs automatically ingest threat feeds.          |
| **AI Reasoning Rail**      | Generate reasoning traces (“why a finding matters”) validated via DeepEval. | ≥ 80% of enriched findings have reasoning traces.        |
| **Trust Centre**           | Sign enriched artifacts and publish to WORM registry.                       | Enriched artifacts signed and traceable by digest.       |
| **Other**                  | Business/compliance mappings to frameworks (e.g., NIST 800-53).             | Completed mapping for at least one major framework.      |


---

### ⚪ **D — Thin Ticket Sync & Human-in-the-Loop**

| **Category**               | **Focus / Deliverables**                                        | **Success Criteria**                                    |
| -------------------------- | --------------------------------------------------------------- | ------------------------------------------------------- |
| **Core Platform**          | Integrate OpenProject or Jira through sync microservice.        | Evidence-URI linking operational for all tickets.       |
| **Orchestration Pipeline** | Automate waiver approval via Tekton pause/resume logic.         | Approval workflow completes with auditable trail.       |
| **AI Reasoning Rail**      | Generate AI-authored remediation summaries and risk narratives. | AI outputs meet ≥ 0.9 faithfulness / ≥ 0.8 consistency. |
| **Trust Centre**           | Store waiver artifacts and co-signed approvals in OCI registry. | 100% of waivers co-signed and logged in Rekor.          |
| **Other**                  | Human-in-the-loop approval logging integrated into dashboard.   | Reviewer co-signatures visible and exportable.          |


---

### ⚪ **E — Assurance Evaluation Loop**

:material-bullseye-arrow: **Goal:** Establish automated assurance gates and reasoning validation feedback.

| **Category**               | **Focus / Deliverables**                                             | **Success Criteria**                                  |
| -------------------------- | -------------------------------------------------------------------- | ----------------------------------------------------- |
| **Core Platform**          | Deploy Policy-as-Code runtime (CUE / OPA).                           | Policy engine operational inside cluster.             |
| **Orchestration Pipeline** | Automated gate evaluation producing signed pass/fail reports.        | Gate results reproducible and versioned per manifest. |
| **AI Reasoning Rail**      | AI analyzes assurance outcomes, validated via DeepEval + Guardrails. | All reasoning outputs validated automatically.        |
| **Trust Centre**           | Sign evaluation outputs and link to manifest digest.                 | 100% of evaluation results signed and timestamped.    |
| **Other**                  | Dashboard visualizes compliance and gate outcomes.                   | Live pass/fail metrics visible in Grafana.            |

---

### ⚪ **F — Continuous Feedback & Drift Detection**

:material-bullseye-arrow: **Goal:** Enable longitudinal tracking of reasoning and assurance quality across versions.

| **Category**               | **Focus / Deliverables**                            | **Success Criteria**                            |
| -------------------------- | --------------------------------------------------- | ----------------------------------------------- |
| **Core Platform**          | Implement Tekton CronJobs for continuous assurance. | Daily drift scans complete successfully.        |
| **Orchestration Pipeline** | Detect SBOM, manifest, and reasoning deltas.        | Alerts generated within SLA (<15 min).          |
| **AI Reasoning Rail**      | Monitor semantic drift (Δ bias, Δ faithfulness).    | Alerts trigger for drift > 5%.                  |
| **Trust Centre**           | Auto-resign and re-timestamp drifted evidence.      | Updated signatures verified in Rekor.           |
| **Other**                  | Slack/Jira notifications for drift events.          | Notifications received for 100% of drift cases. |


---

### ⚪ **G — Governance, Metrics & Reporting**

:material-bullseye-arrow: **Goal:** Establish visibility and accountability across assurance operations.

| **Category**               | **Focus / Deliverables**                         | **Success Criteria**                                  |
| -------------------------- | ------------------------------------------------ | ----------------------------------------------------- |
| **Core Platform**          | Unified Prometheus + Grafana dashboards.         | Dashboards accessible with all key KPIs.              |
| **Orchestration Pipeline** | Monthly “Assurance Report” pipeline.             | Reports generated automatically each month.           |
| **AI Reasoning Rail**      | Include reasoning reliability and drift metrics. | Governance report incorporates AI validation results. |
| **Trust Centre**           | Signed summary artifacts stored immutably.       | 100% cryptographically verified reports.              |
| **Other**                  | Governance review workflow.                      | ≥ 90% report delivery compliance across cycles.       |


---

## Legend 

### 🟢 **Done**        
### 🔵 **In Progress** 
### 🟡 **Planning**    
### ⚪ **Not Started**  

