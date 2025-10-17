# Assurance & Audit

---

#### 1) Access & Scope Definition

> *Review the sanitized findings*

??? info "Click to view"
    ```mermaid
    sequenceDiagram
        participant Auditor
        participant Trust as TrustCentre
        participant Vendor
        participant Customer
        autonumber

        %% Step 1 - Access Request
        Auditor->>Trust: Request read only credentials to portal or API
        Trust->>Auditor: Authenticate using enterprise sign on with multi factor
        Trust->>Auditor: Authorize by role assignment marked as auditor

        %% Step 2 - Manifest Verification
        Auditor->>Trust: Retrieve assurance manifest from trusted storage
        Trust-->>Auditor: Provide manifest with signatures and timestamps
        Auditor->>Vendor: Confirm vendor approval signature
        Auditor->>Customer: Confirm customer approval signature
        Auditor-->>Auditor: Verify manifest is current and represents agreed scope

        %% Step 3 - Scope Mapping
        Auditor->>Auditor: Identify frameworks listed in manifest such as SOC two or ISO twenty seven thousand one
        Auditor->>Auditor: Load framework data into audit checklist tool
        Auditor-->>Trust: Record mapping reference for traceability
    ```


**Actors**

* *Auditor* – Performs an independent review under defined trust conditions.  
* *TrustCentre* – Read-only portal/API exposing signed evidence.  
* *Vendor & Customer* – Co-authors/co-signers of the Assurance Manifest.  

**Actions**

1. *Access Request*  
    - Auditor receives **read-only** credentials to the **TrustCentre** portal or API.  
    - Authentication via **OIDC / SSO**; authorization via **policy-based roles** (e.g., `role=auditor`).  

2. *Manifest Verification*  
    - Auditor pulls the **Assurance Manifest** from the **WORM OCI** (TrustCentre).  
    - Validates **signature**, **timestamp**, and **multi-party approval** (vendor + customer).  
    - Confirms this is the *current and agreed-upon version* of the assurance scope.  

3. *Scope Mapping*  
    - Auditor notes frameworks referenced in the manifest (e.g., **SOC 2**, **ISO 27001**, **AI Bias**).  
    - Loads them into an **audit checklist** tool or GRC system for cross-referencing.  

**Desired Outcomes**

| Outcome | Description |
|----------|-------------|
| **Least-Privilege Access** | Auditor operates with read-only, role-scoped permissions. |
| **Verified Scope** | Co-signed, current manifest is validated before any review. |
| **Framework Alignment** | Scope is mapped to recognized frameworks/controls for traceability. |
| **Immutable Source of Truth** | All scope references come from WORM-backed artifacts in TrustCentre. |

---

#### 2) Provenance & Integrity Verification

> *Confirm findings are legitimate*

??? info "Click to view"
    ```mermaid
    sequenceDiagram
        participant Auditor
        participant Ledger as TrustCentre_Provenance_Ledger
        participant Rekor as Transparency_Log_TSA
        autonumber

        %% Step 1 - Retrieve Provenance Attestations
        Auditor->>Ledger: Query provenance records for build and scan events
        Ledger-->>Auditor: Return attestations with source commit id, container digest, toolchain id, and timestamp proof

        %% Step 2 - Cryptographic Validation
        Auditor->>Auditor: Run local verification command using trust control tool
        Auditor->>Rekor: Request transparency inclusion confirmation
        Rekor-->>Auditor: Provide log entry and timestamp authority record
        Auditor->>Auditor: Validate signatures match trusted keys and attestations match commit data

        %% Step 3 - Cross Reference with Audit Ledger
        Auditor->>Ledger: Inspect linked audit ledger entries
        Ledger-->>Auditor: Return event list and integrity data
        Auditor->>Auditor: Verify entry chain integrity and completeness using hash linking
        Auditor->>Auditor: Confirm time sequence is consistent and increasing
        Auditor-->>Rekor: Report verification summary for archival
    ```


**Actors**

* *Auditor* – Verifies cryptographic provenance and integrity.  
* *TrustCentre Provenance Ledger* – Stores in-toto/SLSA attestations.  
* *Rekor / TSA* – Transparency log and timestamp authority.  

**Actions**

1. *Retrieve Provenance Attestations*  
    - Auditor queries the **Provenance Ledger (in-toto/SLSA)** in TrustCentre.  
    - Each attestation links a build/scan/analysis event to: **source commit hash**, **container digest**, **toolchain ID**, **timestamp authority proof**.  

2. *Cryptographic Validation*  
    - Auditor uses the **TrustCentre verification CLI**:  
      `trustctl verify --manifest manifest.yaml --rekor --oci --intoto`  
    - Verifies: **Cosign** signatures match trusted keys; **Rekor** inclusion proof is valid; **in-toto** attestations match commit hashes.  

3. *Cross-reference with Audit Ledger*  
    - Auditor inspects **Audit Ledger** for:  
      - Event integrity (**entry_hash** chain verification)  
      - Completeness (**no missing event IDs**)  
      - Consistent time ordering (**monotonic timestamps**)  
    - Ledger verification is performed cryptographically using **Merkle proofs**.  

**Desired Outcomes**

| Outcome | Description |
|----------|-------------|
| **End-to-End Provenance** | Every artifact ties back to commits, toolchains, and timestamps. |
| **Cryptographic Integrity** | Independent verification via Cosign, Rekor, and in-toto. |
| **Complete Event History** | No gaps or ordering anomalies in ledger chains. |
| **Tamper Evidence** | Merkle proofs detect alteration attempts. |

---

#### 3) Review of Assurance Evidence

> *Review the audit ledger*

??? info "Click to view"
    ```mermaid
    sequenceDiagram
        participant Auditor
        participant Registry
        participant Ledger
        autonumber

        %% Step 1 - Artifact Inspection
        Auditor->>Registry: Browse evidence registry catalog
        Registry-->>Auditor: List artifacts and metadata
        Auditor->>Registry: Download signed artifacts such as SARIF findings, AI reports, policy results, waiver documents, DeepEval metrics
        Registry-->>Auditor: Return selected artifacts with basic details

        %% Step 2 - Signature and Timestamp Validation
        Auditor->>Auditor: Verify signature chain using cosign keyless or threshold methods
        Auditor->>Auditor: Confirm timestamp proof per r f c three one six one
        Auditor-->>Registry: Record validation outcome for the inspected set

        %% Step 3 - AI Reasoning Traceability
        Auditor->>Auditor: Check AI output schema sequence then prompt then context then model then generated output then evaluation
        Auditor->>Ledger: Query hash records and evaluation scores linked to the artifacts
        Ledger-->>Auditor: Provide hash references and score entries for traceability
        Auditor-->>Registry: Note verified provenance and integrity for audit record
    ```


**Actors**

* *Auditor* – Examines signed evidence and AI reasoning traces.  
* *WORM OCI Evidence Registry* – Stores immutable artifacts.  

**Actions**

1. *Artifact Inspection*  
    - Auditor navigates the **WORM OCI Evidence Registry** (e.g., `oci://trustcentre/org/product@sha256...`).  
    - Downloads/queries signed artifacts: **SARIF findings**, **AI assurance reports**, **policy evaluation results**, **signed waiver documents**, **DeepEval metrics**.  

2. *Signature & Timestamp Validation*  
    - Verifies each artifact’s signature chain (Cosign keyless or threshold).  
    - Confirms **RFC 3161** timestamp proofs.  

3. *AI Reasoning Traceability*  
    - Checks **AI outputs** against their schema: *Prompts → Context → Model → Generated Output → Evaluation*.  
    - Each step has a **recorded hash** and **DeepEval score** logged in the **Audit Ledger**.  

**Desired Outcomes**

| Outcome | Description |
|----------|-------------|
| **Evidence Completeness** | All required artifacts are present and retrievable. |
| **Signed & Time-Bound** | Valid signatures and timestamp proofs on each artifact. |
| **Explainable AI** | Reasoning steps are schema-valid, hashed, and auditable. |
| **Ledger Traceability** | Every artifact maps to ledger entries for confirmation. |

---

#### 4) Policy Compliance Verification

> *Confirm you are meeting your organizational standards*

??? info "Click to view"
    ```mermaid
    sequenceDiagram
        participant Auditor
        participant Gate as Policy_Gate
        participant Ledger
        autonumber

        %% Step 1 - Policy Mapping
        Auditor->>Auditor: Parse manifest policies section
        Auditor->>Auditor: Extract control id, test type, acceptance criteria
        Auditor->>Auditor: Example rules\nno critical vulnerabilities means fail\nbias under threshold means pass

        %% Step 2 - Result Correlation
        Auditor->>Gate: Request evaluation summary for selected controls
        Gate-->>Auditor: Return outcomes marked pass fail or waiver
        Auditor->>Ledger: Query policy results linked to the manifest version
        Ledger-->>Auditor: Provide cryptographic links to evidence references

        %% Step 3 - Human in the Loop Justifications
        Auditor->>Ledger: Retrieve signed waiver records for exceptions
        Ledger-->>Auditor: Return reviewer identity signed time justification and policy link
        Auditor-->>Auditor: Confirm controls and acceptance criteria were enforced
    ```


**Actors**

* *Auditor* – Confirms controls and acceptance criteria were enforced.  
* *Policy Gate* – Evaluates findings vs **Manifest** criteria.  

**Actions**

1. *Policy Mapping*  
    - Auditor parses the manifest’s **`policies:`** section.  
    - Each policy defines `control_id`, `test_type`, and `acceptance_criteria` (e.g., *No Critical CVEs*, *bias < 0.05*).  

2. *Result Correlation*  
    - Auditor queries **Policy Gate** results in the ledger:  
      `trustctl query policy --id SEC-01 --manifest manifest:v1.2`  
    - Each control’s outcome (`pass`, `fail`, `waiver`) is cryptographically linked to **evidence URIs**.  

3. *Human-in-the-Loop Justifications*  
    - Auditor checks **signed waiver records** for any exceptions: reviewer identity (signed), timestamp, justification, policy linkage.  

**Desired Outcomes**

| Outcome | Description |
|----------|-------------|
| **Control-Level Traceability** | Each policy outcome references concrete evidence artifacts. |
| **Objective Criteria** | Pass/fail thresholds from the manifest are consistently applied. |
| **Accountable Exceptions** | Waivers are signed, justified, and auditable. |
| **Policy-to-Evidence Binding** | Outcomes cryptographically link back to artifacts and signers. |

---

#### 5) Reconstructing the Assurance Run

> *Replay the evidence chain for posterity*

??? info "Click to view"
    ```mermaid
    sequenceDiagram
        participant Auditor
        participant Ledger as Audit_Ledger
        autonumber

        %% Step 1 - Run Replay
        Auditor->>Ledger: Request reconstruction of a historical assurance run
        Ledger-->>Auditor: Provide code version, tools, findings, reasoning, and enforced policies
        Auditor->>Auditor: Rebuild historical environment and analysis flow

        %% Step 2 - Audit Evidence Chain
        Auditor->>Auditor: Produce evidence lineage sequence\nManifest to Provenance to Scan to Normalization to AI Reasoning to Policy Gate to Signed Report
        Auditor->>Auditor: Verify each link includes matching hash and signature pair

        %% Step 3 - Compare to Current State
        Auditor->>Ledger: Retrieve current state snapshot for the same system
        Ledger-->>Auditor: Return recent records and digests
        Auditor->>Auditor: Compare historical and current evidence sets
        Auditor->>Auditor: Identify drift, unapproved changes, or missing attestations
        Auditor-->>Ledger: Record comparison summary for forensics archive
    ```


**Actors**

* *Auditor* – Replays historical runs for forensics and regulatory assurance.  
* *Audit Ledger* – Provides event sequences and hashes.  

**Actions**

1. *Run Replay*  
    - Using ledger data, reconstructs an entire historical run (code version, tools, findings, AI reasoning, policies enforced).  

2. *Audit Evidence Chain*  
    - Produces the lineage:  
      `Manifest → Provenance → Scan → Normalization → AI Reasoning → Policy Gate → Signed Report`  
    - Each arrow is backed by a **hash + signature** pair.  

3. *Compare to Current State*  
    - Compares replayed evidence to the current system snapshot to detect **drift**, unapproved changes, or missing attestations.  

**Desired Outcomes**

| Outcome | Description |
|----------|-------------|
| **Forensic Reproduction** | Entire runs can be replayed exactly from immutable records. |
| **Continuity & Consistency** | Differences vs current system are detectable and explainable. |
| **Chain-of-Custody Proof** | Every stage is linked and signed across the pipeline. |
| **Regulatory Readiness** | Replay artifacts satisfy regulator requests efficiently. |

---

#### 6) Report Generation & Attestation

> *(Auditor ↔ TrustCentre Transparency Log ↔ Vendor/Customer)*

??? info "Click to view"
    ```mermaid
    sequenceDiagram
        participant Auditor
        participant Centre as TrustCentre
        participant Ledger as Audit_Ledger
        autonumber

        %% Step 1 - Compile Findings
        Auditor->>Auditor: Summarize verified controls and exceptions
        Auditor->>Auditor: Include waiver notes, confidence metrics, and digest list
        Auditor-->>Auditor: Prepare data set for attestation report

        %% Step 2 - Generate Audit Attestation
        Auditor->>Auditor: Create digitally signed assurance attestation report
        Auditor->>Auditor: Reference manifest, artifact hashes, and transparency proofs
        Auditor-->>Auditor: Store report within the auditor managed repository

        %% Step 3 - Anchor to TrustCentre
        Auditor->>Centre: Submit signed attestation for anchoring
        Centre->>Ledger: Record attestation reference and timestamp
        Ledger-->>Centre: Return confirmation log entry
        Centre-->>Auditor: Provide verification link and anchored record summary
    ```


**Actors**

* *Auditor* – Issues a signed Assurance Attestation Report (AAR).  
* *TrustCentre* – Anchors and exposes the attestation for verification.  

**Actions**

1. *Compile Findings*  
    - Summarizes: **verified controls**, **exceptions/waivers**, **confidence metrics** (DeepEval, AI consistency), **digest list**.  

2. *Generate Audit Attestation*  
    - Produces a **digitally signed AAR** referencing manifest, artifact hashes, Rekor proofs (stored in auditor’s OCI repo).  

3. *Anchor to TrustCentre*  
    - Submits AAR to the **TrustCentre Transparency Log**; Rekor log ID and timestamp appended to the **Audit Ledger**.  

**Desired Outcomes**

| Outcome | Description |
|----------|-------------|
| **Signed Auditor Statement** | Independent attestation is cryptographically verifiable. |
| **Discoverable Proof** | AAR visible via Transparency Log for customers/regulators. |
| **Tight Ledger Integration** | Attestation linked to vendor’s immutable audit history. |
| **Portable Verification** | Proof can be verified without trusting vendor infrastructure. |

---

#### 7) Continuous Monitoring & Trust Renewal

> *(Auditor ↔ Event Feeds ↔ Rekor/TSA ↔ TrustCentre)*

??? info "Click to view"
    ```mermaid
    sequenceDiagram
        participant Auditor
        participant Centre as TrustCentre
        participant Ledger
        autonumber

        %% Step 1 - Scheduled Audit Hooks
        Auditor->>Centre: Subscribe to assurance event feed using webhook
        Centre-->>Auditor: Confirm subscription for continuous updates
        Centre->>Auditor: Send notification for each new assurance run
        Auditor->>Ledger: Verify ledger root hash automatically
        Ledger-->>Auditor: Return validation result

        %% Step 2 - Trust Drift Detection
        Centre->>Auditor: Alert when manifest or signing key changes detected
        Auditor->>Ledger: Request difference report since last attestation
        Ledger-->>Auditor: Provide evidence change summary and updated hashes
        Auditor->>Auditor: Review alert and confirm trust drift analysis

        %% Step 3 - Periodic Trust Reanchoring
        Auditor->>Centre: Initiate scheduled reverification cycle
        Centre->>Ledger: Supply current ledger roots and proof references
        Ledger-->>Auditor: Return verification data set for archival
        Auditor->>Auditor: Validate roots and transparency proofs for preservation
    ```


**Actors**

* *Auditor* – Subscribes to ongoing assurance signals.  
* *TrustCentre* – Emits event feeds and ledger roots for verification.  

**Actions**

1. *Scheduled Audit Hooks*  
    - Auditor subscribes to **assurance event feeds** (webhooks).  
    - Each new run’s **ledger root hash** is verified automatically.  

2. *Trust Drift Detection*  
    - Alerts on **manifest** or **signing key** changes.  
    - Ledger *diff* reports show evidence changes since last attestation.  

3. *Periodic Trust Re-Anchoring*  
    - Every **30/90 days**, auditor re-verifies **ledger roots** and **Rekor** proofs for long-term preservation.  

**Desired Outcomes**

| Outcome | Description |
|----------|-------------|
| **Always-On Assurance** | Posture is continuous, not annual. |
| **Early-Warning Signals** | Drift/key-rotation alerts trigger proactive reviews. |
| **Durable Verifiability** | Regular re-anchoring maintains long-term integrity. |
| **Low-Touch Governance** | Automation reduces manual overhead while increasing trust. |

---


---

## End State 

> *(Verifiable Outcome)*

 "diagrams/workflows/basic-assurance.mmd"
```

**Desired Outcomes**

| Outcome | Description |
|----------|-------------|
| **Machine-Readable Attestation** | Auditor issues a signed, verifiable report of control outcomes. |
| **Cryptographically Anchored** | Integrity proven via **Sigstore**, **Rekor**, and **Audit Ledger**. |
| **Zero Additional Trust Assumptions** | Verification possible without the vendor’s pipeline. |
| **Repeatable & Replayable** | Future audits can replay runs and confirm assurance continuity. |

#### Auditor Value Summary

> *(Outcomes Comparison)*

agrams/workflows/basic-assurance.mmd"
```

**Desired Outcomes**

| Dimension | Old Way | New Continuous Assurance Way |
|----------|---------|-------------------------------|
| **Evidence Validation** | Manual screenshots, spreadsheets | Cryptographic proof chain (Rekor, OCI, Ledger) |
| **Scope Control** | Static compliance matrix | Live manifest defining required proofs |
| **Audit Cadence** | Reactive, once a year | Continuous, trust-anchored visibility |
| **Independence** | Based on vendor statements | Verified against immutable TrustCentre data |
| **Non-Repudiation** | None | Built-in with Sigstore, WORM, TSA, KMS |


---

**Copy Block (for narrative use)**

“All declared controls were tested, verified, and logged immutably.  
 The integrity of each artifact is cryptographically proven via Sigstore, Rekor, and the TrustCentre Audit Ledger.  
 No further manual trust is required.”
