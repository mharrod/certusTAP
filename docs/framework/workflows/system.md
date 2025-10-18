# Core Processes
---
#### 1) Assurance Manifest Defintion

> *Declaring what trusted looks like*

??? info "Click to view"
    ```mermaid
    sequenceDiagram
        participant Customer
        participant Vendor
        participant TrustCentre
        autonumber

        Customer->>Vendor: Initiate assurance discussion
        Vendor-->>Customer: Propose applicable frameworks
        Customer->>Vendor: Confirm scope (ISO27001, SOC2, NIST, SLSA, AI Assurance)
        Vendor->>Vendor: Incorporate threat-model findings

        Customer->>Vendor: Co-author Assurance Manifest
        Vendor-->>Customer: Draft for review
        Customer->>TrustCentre: Submit baseline manifest for registration
        TrustCentre-->>Customer: Acknowledge and timestamp
        TrustCentre-->>Vendor: Confirm manifest visibility/version
        Vendor-->>Customer: Apply updates and finalize
        Customer->>TrustCentre: Publish Manifest v1.0
        TrustCentre-->>Customer: Immutable record created
        TrustCentre-->>Vendor: Manifest available to pipeline
    ```

**Actors**

* *Customer / Procurer* – requires proof the system meets security, privacy, and AI assurance requirements.  
* *Vendor / Provider* – owns the system under test and evidence generation.

**Actions**

1. *Joint Framework Alignment*
    - Customer and Vendor agree on which frameworks apply: e.g., *ISO 27001, SOC 2, NIST 800-53, SLSA, AI-Assurance (bias / privacy / drift)*.  
    - Each requirement maps to measurable controls and test criteria (e.g., “no critical CVEs,” “model fairness threshold > 0.9”).  
    - Threat-modelling findings could be incorporated.

2. *Author Assurance Manifest*
    - Both parties collaboratively define an **Assurance Manifest** (language e.g. YAML / JSON / Markdown / CUE TBD).  
    - Manifest lists:  
        - required proofs (security, integrity, privacy, AI behavior)  
        - scan schedule (e.g., nightly, release-gated)  
        - success / failure thresholds  
        - responsible parties and evidence sinks.

**Desired Outcomes**

| Outcome | Description |
|----------|--------------|
| Mutual Assurance Understanding | Customer and Vendor share a unified interpretation of what “assured” means for this system — including scope, frameworks, and testing expectations. |
| **Framework-to-Control Mapping** | Each selected framework requirement (e.g., ISO 27001 A.12.6.1 or NIST 800-53 RA-5) is mapped to measurable, testable controls within the system. |
| **Defined Success Criteria** | Quantitative pass/fail thresholds are explicitly defined — e.g., “no critical CVEs,” “model fairness > 0.9,” or “zero privacy violations.” |
| **Threat Model Integration** | Threat-modelling findings are aligned with assurance tests so high-risk attack paths are continuously covered by scanning or attestations. |
| **Signed Assurance Manifest** | A machine-readable, digitally signed manifest defines what will be tested, how often, by which tools, and where evidence will be stored. |
| **Immutable Provenance Record** | An in-toto attestation records the manifest’s signature, timestamp, and authorship for auditability inside the TrustCentre. |
| **Role Accountability Assignment** | Both parties have clear ownership of assurance activities such as scanning, sign-off, and evidence review. |
| **Trust Baseline Established** | The measurable baseline for ongoing automated assurance is now defined — future scans and attestations will compare against this state. |

---

#### 2) Scheduled Assurance Execution

> *Running an assurance check*

??? info "Click to view"
    ```mermaid
    sequenceDiagram
        participant ORCH as Pipeline_Orchestrator
        participant SUT as Systems_Under_Test
        participant OCI as TrustCentre_OCI
        participant RAG as AI_Retrievers
        autonumber

        %% Manifest Retrieval
        ORCH->>OCI: Pull latest Assurance Manifest
        OCI-->>ORCH: Manifest + signature + timestamp
        ORCH->>ORCH: Verify signature and timestamp
        Note right of ORCH: Manifest verified

        %% Scanning Phase
        ORCH->>SUT: Run scanners (OpenGrep, Trivy, Presidio, Checkov, SCA)
        SUT-->>ORCH: JSON, SARIF, logs
        ORCH->>SUT: Run AI probes (drift, bias, prompt-safety)
        SUT-->>ORCH: Probe results
        ORCH->>ORCH: Hash outputs + record versions

        %% Raw Ingestion into RAG Memory
        ORCH->>RAG: Index code corpus + scan outputs
        RAG-->>ORCH: Ingest complete

        %% Evidence (optional)
        ORCH->>OCI: Push artifacts + metadata (immutable)
    ```




**Actors**

* *Pipeline Orchestrator* – Executes scheduled workflows using systems such as GitHub Actions, Dagger, or Tekton.  
* *Systems Under Test* – Code repositories, applications, and services being continuously validated.  

**Actions**

1. *Manifest Retrieval*  
    - On the defined cadence, the orchestrator **pulls the latest Assurance Manifest** from the TrustCentre’s OCI.  
    - Orchestrator verifies digital signature and timestamp before execution.  

2. *Scanning Phase*  
    - Executes declared tools:  
        - e.g., *OpenGrep*, *Trivy*, *Presidio*, *Checkov*, or *SCA* for code, IaC, and privacy scanning.  
        - e.g., *Custom AI assurance probes* for drift, bias, and prompt safety validation.  
    - Collects raw outputs (JSON, SARIF, logs) and hashes them for provenance integrity.  

3. *Raw Ingestion into RAG Memory*  
    - Both the *raw code corpus* and *raw scan results* are indexed into the **AI Reasoning Rail’s retrievers** for semantic enrichment and historical traceability.  

**Desired Outcomes**

| Outcome | Description |
|----------|-------------|
| **Automated Cadence Enforcement** | Assurance tests execute on a defined schedule or event trigger, ensuring continuous visibility without manual intervention. |
| **Verified Manifest Integrity** | Each run confirms the authenticity and version of the Assurance Manifest before initiating tests. |
| **Multi-Domain Scanning Coverage** | Security, privacy, and AI behavior scans run in a single orchestrated flow with unified evidence capture. |
| **Provenance Preservation** | Every scan result is hashed, timestamped, and linked to its manifest and tool version for auditability. |
| **RAG Memory Integration** | Raw results are ingested into the AI Reasoning Rail’s retrievers to enable semantic recall, enrichment, and longitudinal assurance analysis. |
| **Continuous Assurance Loop** | Establishes a foundation for autonomous, trust-based validation that feeds future assurance reports and reasoning cycles. |


#### 3) Normalization & Contextualization

> *Make the data more useful*

??? info info "Click to view"
    ```mermaid
    sequenceDiagram
        participant NORM as Normalization_Component
        participant AI as AI_Retrievers_Fusion_Engine
        autonumber

        %% Format Normalization
        NORM->>NORM: Convert raw scanner outputs to SARIF v2.1.0
        NORM->>NORM: Extract rule ID, severity, file path, line range, message
        Note right of NORM: Creates consistent schema for cross-tool comparability

        %% Fingerprinting & Deduplication
        NORM->>NORM: Compute stable hashes per finding (rule ID + line context)
        NORM->>NORM: Deduplicate across scanners and prior runs
        Note right of NORM: Ensures stable tracking of recurring issues

        %% Severity Mapping
        NORM->>NORM: Map tool-specific severities to unified scale (Critical→High→Medium→Low)
        Note right of NORM: Standardized severity aids downstream analysis

        %% Contextual Augmentation
        NORM->>AI: Send normalized and deduped findings
        AI->>AI: Retrieve related code snippets, commit messages, manifest clauses
        AI->>AI: Perform embedding search and semantic grouping
        AI-->>NORM: Return enriched, context-aware findings
    ```



**Actors**

* *Normalization Component* – Standardizes raw outputs into a consistent schema for cross-tool comparability.  
* *AI Retrievers / Fusion Engine* – Enrich findings with contextual and semantic metadata to improve downstream analysis.  

**Actions**

1. *Format Normalization*  
    - Converts scanner output into **standard SARIF v2.1.0** schema.  
    - Extracts common fields: rule ID, severity, file path, line range, and message.  

2. *Fingerprinting & Deduplication*  
    - Computes **stable hashes** per finding (rule ID + line context).  
    - Deduplicates across scanners and previous runs using hash comparison to ensure stable tracking.  

3. *Severity Mapping*  
    - Normalizes all tool-specific severities to a unified scale (e.g., *Critical → High → Medium → Low*).  

4. *Contextual Augmentation*  
    - AI Retrievers pull nearby source code snippets, commit messages, and manifest clauses related to each finding.  
    - Embedding search enables **semantic grouping** of similar issues for cross-scan reasoning.  

**Desired Outcomes**

| Outcome | Description |
|----------|-------------|
| **Consistent Data Structure** | All scanner outputs are normalized to the SARIF standard, ensuring compatibility across tools and systems. |
| **Duplicate-Free Findings** | Fingerprinting guarantees stable identifiers for findings, eliminating redundancy across scans and runs. |
| **Unified Severity Model** | Findings from different scanners are mapped to a single, normalized severity scale for accurate prioritization. |
| **Contextually Enriched Evidence** | Each finding is paired with relevant source code, commit context, and assurance manifest references. |
| **Semantic Grouping** | Related issues are clustered through embedding similarity, improving reasoning and triage efficiency. |
| **TrustCentre-Ready Data** | Clean, deduplicated, and semantically enriched records are prepared for storage, visualization, and policy evaluation in the TrustCentre. |

#### 4) Enrichment (Threat, Assurance & Policy)

> *Enrich the data to make it more meaningful*

??? info "Click to view"
    ```mermaid
    sequenceDiagram
        participant ENR as Enrichment_Service
        participant AI as AI_Fusion_Logic_LLM_Chain
        autonumber

        %% Threat Enrichment
        ENR->>ENR: Lookup CVSS, CWE, CPE, EPSS, CISA KEV, patch data
        ENR->>AI: Provide enriched findings
        AI->>AI: Re-rank findings by context and exploitability
        Note right of AI: Context-aware prioritization based on enrichment metadata

        %% AI Contextual Reasoning
        AI->>AI: Analyze findings vs Assurance Manifest goals
        AI->>AI: Generate human-readable summaries + remediation guidance
        Note right of AI: Aligns results with assurance and mitigation objectives

        %% AI Safety & Schema Gate
        AI->>AI: Validate outputs against expected JSON schema
        AI->>AI: Run DeepEval / Guardrails for faithfulness + consistency checks
        Note right of AI: Ensures outputs are trustworthy and structured

        %% Policy Decision Preparation
        AI->>ENR: Return policy-ready findings
        ENR->>ENR: Tag each finding with pass / fail / waiver
        ENR->>ENR: Format data for downstream Policy Gate
    ```


**Actors**

* *Enrichment Service* – Performs correlation, lookup, and contextual threat analysis.  
* *AI Fusion Logic / LLM Chain* – Conducts reasoning, summarization, and policy preparation.  

**Actions**

1. *Threat Enrichment*  
    - Looks up CVSS, CWE, CPE, EPSS, CISA KEV, and patch availability.  
    - The AI Fusion Engine re-ranks findings by context and exploitability.  

2. *AI Contextual Reasoning*  
    - The LLM Chain analyzes findings against **Assurance Manifest** goals.  
    - Generates human-readable summaries and recommended remediation actions.  

3. *AI Safety & Schema Gate*  
    - Structured outputs are validated against expected JSON schemas.  
    - Tools such as **DeepEval** or **Guardrails** run faithfulness and consistency checks.  

4. *Policy Decision Preparation*  
    - Each finding is tagged with a preliminary policy outcome: `pass | fail | waiver`.  
    - Data is formatted for downstream Policy Gate evaluation.  

**Desired Outcomes**

| Outcome | Description |
|----------|-------------|
| **Threat Intelligence Integration** | Findings are enriched with real-world exploit data and threat context (CVSS, EPSS, KEV). |
| **Context-Aware Reasoning** | AI Fusion aligns findings with Assurance Manifest objectives to generate meaningful insights. |
| **Validated AI Outputs** | Schema and consistency checks ensure reasoning results are structurally correct and faithful. |
| **Policy-Ready Data** | Findings are pre-labeled for automated evaluation, reducing human review load. |
| **Enhanced Prioritization** | Issues are ranked by exploitability and assurance relevance, improving focus and response time. |
| **Assurance-Linked Context** | Each enriched record maintains its provenance and linkage to manifest controls. |

---

#### 5) Policy Gate & Human-in-the-Loop

> *Double check that the outputs are legitimate*

??? info "Click to view"
    ```mermaid
    sequenceDiagram
        participant PolicyEngine
        participant HumanReviewers
        participant AuditLedger
        autonumber

        PolicyEngine->>PolicyEngine: Evaluate findings vs Manifest criteria
        PolicyEngine->>PolicyEngine: Rule: No Critical CVEs = Fail
        PolicyEngine->>PolicyEngine: Rule: Bias under threshold = Pass

        PolicyEngine->>HumanReviewers: Route low-confidence findings (confidence under 85 percent)
        HumanReviewers->>HumanReviewers: Review, waive, approve, or reject
        HumanReviewers-->>PolicyEngine: Return decision and rationale

        PolicyEngine->>AuditLedger: Log automated results
        HumanReviewers->>AuditLedger: Log manual results (signatures, timestamps)
        AuditLedger-->>PolicyEngine: Return policy hash and audit reference
    ```



**Actors**

* *Policy Engine (CUE / OPA)* – Applies machine-enforceable logic to evaluate compliance.  
* *Human Reviewers / Approvers* – Provide oversight for exceptions, uncertainty, and critical waivers.  

**Actions**

1. *Automated Policy Evaluation*  
    - Policy-as-code logic compares each finding to **Manifest criteria**.  
    - Examples: “No Critical CVEs → Fail,” “All AI bias tests < 0.05 → Pass.”  

2. *Human Review Trigger*  
    - If AI or policy confidence < threshold (e.g., 0.85), issue is routed to the **Human-in-the-Loop Panel**.  
    - Reviewers can waive, approve, or reject findings with justification.  

3. *Audit Recording*  
    - Every decision (automated or manual) is **logged to the Audit Ledger** with signatures, timestamps, and policy hashes.  

**Desired Outcomes**

| Outcome | Description |
|----------|-------------|
| **Automated Policy Enforcement** | Objective criteria are applied at scale with transparent, machine-readable logic. |
| **Human Oversight for Edge Cases** | Human reviewers handle low-confidence or exception scenarios for accountability. |
| **Immutable Audit Trail** | All actions are recorded with cryptographic evidence and versioned policy states. |
| **Waiver Traceability** | Every manual exception is logged and linked to an auditable approval record. |
| **Balanced Governance** | Automation accelerates compliance while preserving human judgment where needed. |
| **Feedback Loop to AI Models** | Human decisions are re-ingested into the AI Reasoning Rail for model improvement. |

---

#### 6) Evidence Finalization & Signing

> *Sign and attest the findings*

??? info "Click to view"
    ```mermaid
    sequenceDiagram
        participant GATE as Policy_Gate_Sign_Verify_Service
        participant TSA as Time_Stamp_Authority
        participant LED as Audit_Ledger
        participant CUST as Customer
        autonumber

        %% Report Assembly
        GATE->>GATE: Aggregate SARIF, AI summaries, policy verdicts, approvals
        GATE->>GATE: Build Final Assurance Report
        Note right of GATE: Unified evidence package for signing

        %% Evidence Signing & Publication
        GATE->>GATE: Sign report with TrustCentre-managed keys
        GATE->>TSA: Request timestamp proof
        TSA-->>GATE: Return trusted timestamp
        GATE->>LED: Record artifact digest, signer, timestamp, metadata
        GATE->>GATE: Publish signed report to WORM OCI (Write Once Read Many)
        Note right of GATE: Rekor transparency log entry created

        %% Feedback to Customer
        GATE-->>CUST: Provide URI to signed artifact
        CUST->>CUST: Verify signature via Sigstore/Rekor CLI
        Note right of CUST: Independent verification ensures trust and transparency
    ```


**Actors**

* *Policy Gate / TrustCentre Sign-Verify Service* – Aggregates assurance artifacts and performs digital signing.  

**Actions**

1. *Report Assembly*  
    - Combines all results (SARIF, AI recommendations, policy verdicts, approvals) into a **Final Assurance Report**.  

2. *Evidence Signing & Publication*  
    - The report is digitally signed using **TrustCentre-managed keys**.  
    - Published to the **WORM OCI** (Write Once Read Many).  
    - Logged to **Rekor** and timestamped by a **Time Stamping Authority (TSA)**.  
    - The Audit Ledger records artifact digest, signer, and result metadata.  

3. *Feedback to Customer*  
    - Customer receives a URI to the signed artifact.  
    - Verification can be performed via **Sigstore / Rekor CLI**, independently of the vendor’s pipeline.  

**Desired Outcomes**

| Outcome | Description |
|----------|-------------|
| **Comprehensive Assurance Report** | A complete record of tests, findings, and approvals is compiled into a single signed artifact. |
| **Cryptographically Signed Evidence** | Digital signatures ensure authenticity, integrity, and non-repudiation of assurance results. |
| **Tamper-Evident Publication** | Reports are stored immutably in a WORM registry with transparency logs. |
| **Independent Verification** | Customers can verify signatures and timestamps without relying on vendor systems. |
| **Traceable Provenance Chain** | Every artifact links back to its manifest, scanner, policy, and signer identity. |
| **Immutable Assurance Ledger** | A permanent, auditable record of the entire assurance lifecycle is maintained. |

---

#### 7) Metrics, Visualization & Continuous Learning

> *Interact and learn from the outcomes*

??? info "Click to view"
    ```mermaid
    sequenceDiagram
        participant MET as Metrics_Service
        participant DASH as Dashboard
        participant AI as AI_Analyst_Module
        participant LED as Audit_Ledger
        autonumber

        %% Data Aggregation
        MET->>MET: Ingest ledger and report data
        MET->>MET: Transform and store in metrics warehouse (Prometheus / Grafana / BigQuery)
        Note right of MET: Aggregates assurance artifacts for analysis

        %% Visualization
        MET->>DASH: Provide compliance, trend, and accuracy datasets
        DASH->>DASH: Render compliance status, MTTA, assurance trends
        Note right of DASH: Visualizes KPIs and historical assurance metrics

        %% RAG Memory Refinement
        MET->>AI: Send historical findings and outcomes
        AI->>AI: Analyze false positives, waived findings, drift patterns
        AI->>AI: Update retriever index and fusion weights
        Note right of AI: Improves future assessments via continuous learning

        %% Trust Re-Anchoring
        MET->>LED: Re-sign and re-anchor ledger roots
        LED-->>MET: Confirm Rekor / blockchain timestamp
        Note right of LED: Ensures long-term verifiable continuity of trust
    ```


**Actors**

* *Metrics Service* – Aggregates and transforms assurance data for analysis.  
* *Dashboard* – Visualizes compliance and performance metrics.  
* *AI Analyst Module* – Continuously refines models and retrievers based on historical outcomes.  

**Actions**

1. *Data Aggregation*  
    - Ingests ledger and report data into a metrics warehouse (e.g., *Prometheus*, *Grafana*, or *BigQuery*).  

2. *Visualization*  
    - Displays compliance status, assurance trends, mean-time-to-assure (MTTA), and AI accuracy over time.  

3. *RAG Memory Refinement*  
    - AI system learns from historical patterns (e.g., false positives, waived findings).  
    - Updates retriever index and fusion weights to improve future assessments.  

4. *Trust Re-Anchoring*  
    - Periodically re-signs and re-anchors ledger roots to transparency services (e.g., *Rekor*, blockchain).  
    - Ensures long-term non-repudiation and verifiable continuity of trust.  

**Desired Outcomes**

| Outcome | Description |
|----------|-------------|
| **Operational Insight** | Dashboards provide visibility into assurance posture, trends, and performance indicators. |
| **Continuous Model Improvement** | AI systems adapt based on historical data to enhance reasoning accuracy. |
| **Reduced False Positives** | Feedback loops filter noise and improve signal quality in findings. |
| **Transparency Anchoring** | Ledger re-signing maintains long-term verifiability across audit periods. |
| **Actionable Metrics** | Quantitative metrics enable teams to prioritize improvements and track MTTA / assurance SLAs. |
| **Adaptive Assurance System** | The entire pipeline evolves toward higher trust and efficiency through data-driven learning. |

---

## End-State: Verifiable Trust Loop

Every artifact—manifest, scan result, AI analysis, and final report—is:

* **Signed by verified identities**,  
* **Timestamped and immutable**,  
* **Traceable through the Audit Ledger and Rekor**,  
* **Linked to the original Assurance Manifest** defining the expected controls.  

**Desired Outcomes**

| Outcome | Description |
|----------|-------------|
| **End-to-End Verifiability** | Each assurance artifact is cryptographically linked, signed, and auditable. |
| **Customer Assurance Confidence** | Customers can independently verify that agreed-upon assurance criteria were met on a given date. |
| **Vendor Proof of Diligence** | Vendors demonstrate that every control was tested and logged in a tamper-evident system. |
| **Mutual Trust Framework** | Both parties share a provable assurance baseline governed by transparent evidence. |
| **Immutable Trust Ledger** | The system provides a continuous, cryptographic chain of custody for all assurance activities. |
| **Assured System Integrity** | Completes the feedback loop between evidence, policy, and verification — forming a persistent trust ecosystem. |

---
