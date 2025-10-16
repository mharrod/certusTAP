# Roadmap
A forward roadmap integrating AI reasoning, evidence immutability, and human oversight into every stage of the assurance pipeline. We will follow a Crawl-->Walk-->Run-->Fly approach.

---

??? info "**Crawl — Exploration & Foundational R&D**"
    **Purpose:**  
    Build the conceptual and technical scaffolding for the assurance ecosystem.  
    This is the R&D-heavy phase where experimentation and discovery dominate.

    **Focus Areas:**

    - R&D on assurance primitives (provenance, evidence registries, policy engines, DeepEval metrics)
    - Prototype pipelines for signed evidence and AI reasoning validation
    - Early integration of Haystack + Presidio + Ollama for privacy and explainability checks
    - Definition of schemas and manifests for signed artifacts and trust proofs
    - Highly adhoc, fragile, and manually driven workflows suited for technophiles

    **Outcomes:**

    - Early PoCs of signing, provenance, and reasoning traceability
    - Foundational documentation of design decisions and architecture
    - Developer-centric CLI scripts and reproducible Dagger pipelines
    - Knowledge capture to guide structured development in the next phase

---

??? info "**Walk — Structured Development & CLI-Driven Workflows**"
    **Purpose:**  
    Transform experimentation into structured, reproducible, and developer-usable assurance pipelines.

    **Focus Areas:**

    - CLI tooling (`certus tap verify`, `certus tap evaluate`) for local/test use
    - Consistent artifact schemas (SARIF, JSON, policy outputs)
    - Early CI/CD pipelines integrating Cosign + RFC 3161 timestamps
    - SME-ready local deployments for assurance validation
    - Shift from discovery to repeatable trust workflows

    **Outcomes:**

    - Reliable CLI tools and containerized environments
    - Documented schema standards and reproducible builds
    - Integration testing and internal adoption by SMEs
    - Early alpha releases and structured feedback loops

---

??? info "**Run — Defined Scope & Production-Ready Capabilities**"
    **Purpose:**  
    Operationalize core assurance services for controlled production deployment by SMEs and auditors.

    **Focus Areas:**

    - Clearly defined assurance scope and use cases (AI model assurance, policy validation, waiver management)
    - Hardened APIs and registry integrations (Audit Ledger, WORM OCI Evidence Store)
    - Governance enforcement — signature chains, waivers, audit logs
    - Observability and reliability metrics integrated into CI/CD
    - Security, integrity, and compliance validation embedded by design

    **Outcomes:**

    - Versioned and stable Certus TAP modules
    - SMEs can deploy selected capabilities to production
    - End-to-end traceability across evidence, policies, and reasoning traces
    - Dashboards visualizing compliance, drift, and assurance performance

---

??? info "**Fly — Autonomous, Trust-Centric Ecosystem**"
    **Purpose:**  
    Deliver a production-grade Assurance Platform uniting human and AI participants in continuous, explainable trust loops.

    **Focus Areas:**

    - Unified web/UI and API experience for non-technical users
    - Continuous AI-in-the-loop assurance and governance automation
    - Multi-tenant, federated deployments with inter-org attestation
    - Governance dashboards, transparency reports, and human co-signatures
    - Self-service onboarding and policy management

    **Outcomes:**

    - Full "Assurance as a Service" offering
    - Immutable, cross-org evidence registries and reasoning reports
    - AI and human decision-making transparently co-signed and auditable
    - Enterprise-grade scalability, resilience, and trust certification readiness

---

## **Crawl-to-Walk**

The project is currently in **Crawl** and moving towards **Walk**, we are focusing on:

- Open-ended R&D and architectural experimentation  
- Iterative prototyping with reproducibility and immutability foundations  
- Designing schemas, manifests, and reasoning validation frameworks  
- Preparing the transition to **Walk** with CLI and CI/CD integration groundwork


### A0 — Foundation & Evidence Layer

**Goal:** Establish reproducible environments, baseline security hygiene, and immutable storage foundations.

**Deliverables:**
- LocalStack & OCI evidence registry setup  
- Signed artifacts and attestations (Cosign)  
- Dagger-based reproducible pipelines  
- Foundational assurance manifest (YAML schema)

**AI Reasoning Integration:**
- None required at this stage; focus on reproducibility for future AI traceability.

---

### **A1 — Normalization & Schema Standardization**

**Goal:** Build the unified schema for assurance data across scanners, enrichers, and policy gates.

**Deliverables:**

- Normalization engine emitting SARIF + JSON  
- Fingerprinting and severity mapping  
- Schema registry for scanner outputs

**AI Reasoning Integration:**

- Introduce structured prompt schema for future reasoning steps.  
- Map scanner findings to explainable categories for contextual analysis.

---

### **B1 — Contextual Enrichment & Threat Intelligence**

**Goal:** Enrich normalized findings with contextual metadata and threat intelligence feeds.

**Deliverables:**
- Contextual enrichment (service, component, environment)  
- Threat enrichment (EPSS, KEV, CVSS, CWE)  
- Business and compliance mappings

**AI Reasoning Integration:**
- Begin reasoning trace generation for enrichment logic (“why a finding matters”).  
- AI produces draft rationales that are validated by DeepEval for faithfulness and accuracy.

---

### **B2 — Thin Ticket Sync & Human-in-the-Loop**

**Goal:** Seamlessly integrate findings into existing workflow tools (Jira, ServiceNow) with lightweight sync logic.

**Deliverables:**
- Ticket sync microservice (create/update logic)  
- Evidence URIs embedded in tickets  
- Basic waiver approval workflow

**AI Reasoning Integration:**
- AI Reasoning Rail generates remediation summaries and risk narratives for each issue.  
- Outputs are verified for *faithfulness* (≥ 0.9) and *consistency* (≥ 0.8) before human review.  
- Approvers can co-sign AI-authored content with audit trails logged in OCI.

---

### **C1 — Assurance Evaluation Loop**

**Goal:** Establish automated assurance gates and reasoning validation feedback.

**Deliverables:**
- Policy-as-Code framework (CUE/Rego)  
- Evaluation engine producing signed results  
- Dashboard showing rule compliance metrics

**AI Reasoning Integration:**
- AI Reasoning Rail analyzes assurance outcomes to produce explainable summaries.  
- DeepEval, Guardrails, and Promptfoo enforce accuracy and safety on reasoning chains.  
- Each output links reasoning traces → model ID → validation metrics → signature.

---

### **C2 — Continuous Feedback & Drift Detection**

**Goal:** Enable longitudinal tracking of reasoning and assurance quality across versions.

**Deliverables:**
- Drift detection for SBOM, manifest, and reasoning deltas  
- Continuous assurance job scheduler  
- Slack/Jira alert integration

**AI Reasoning Integration:**
- Semantic drift analysis on AI reasoning traces (Δ bias, Δ faithfulness).  
- Model behavior and bias delta visualized in Grafana dashboards.  
- Automated retraining triggers for significant reasoning drift (>5%).

---

### **D1 — Governance, Metrics & Reporting**

**Goal:** Establish visibility and accountability across assurance operations.

**Deliverables:**
- Unified dashboard (Prometheus + Grafana)  
- Monthly Assurance Report pipeline  
- Signed summary artifacts in OCI

**AI Reasoning Integration:**
- Governance dashboards include reasoning reliability metrics (faithfulness, safety, bias drift).  
- Human validators co-sign any AI-influenced decision paths.  
- Transparency index reports generated from reasoning provenance data.

---

### **E1 — Federated Trust & Inter-Org Attestation**

**Goal:** Extend assurance verification across organizations and domains.

**Deliverables:**
- Federation layer for replayable attestations  
- Shared trust anchors (Sigstore, Rekor, SLSA provenance)  
- Multi-tenant policy validation APIs

**AI Reasoning Integration:**
- AI Reasoning Rail evaluates and summarizes federated attestation trust chains.  
- Provides narrative proofs of cross-org validation that are cryptographically linked to underlying evidence.  
- DeepEval ensures factual grounding against signed provenance data.

---

## Key Metrics & KPIs

| Metric | Description | Target |
|---------|-------------|--------|
| **Reasoning Faithfulness** | AI output alignment with verified evidence | ≥ 0.9 |
| **Explainability Coverage** | % of outputs with structured reasoning trace | 100% |
| **Bias Drift Delta** | % change in bias or fairness metrics | ≤ 5% |
| **Reasoning Consistency** | Agreement across identical contexts | ≥ 0.8 |
| **Human Validation Coverage** | % of AI outputs co-signed by human reviewers | ≥ 30% |
| **Signature Freshness** | % of artifacts re-signed within SLA | 100% |
| **Evidence Completeness** | Findings with valid OCI link | 100% |

---

## Governance & Reporting Enhancements

- Every milestone produces **signed reasoning traces** alongside assurance artifacts.  
- Monthly “Assurance & Reasoning Reliability” report includes:
  - DeepEval & Guardrails metrics
  - Reasoning drift graphs
  - Reviewer co-signature statistics
  - Cross-org reasoning trace validation summary

**Outcome:**  
By embedding reasoning transparency throughout — not as a bolt-on — the CAP platform achieves *human-auditable AI reasoning* and *machine-verifiable trust proofs* simultaneously.

---

