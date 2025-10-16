# Construction Use Case

This use case is a working-in-progress example of how the assurance framework and logic can be leveraged as part of a non-IT or cyber-physical system. While we are not necessarily building this out, the goal is to ensure that our framework can map to real-world domains where safety, integrity, and verifiable compliance are critical.

---
??? info "Click to view Construction Assurance Flow (Mermaid Diagram)"
    ```mermaid
      flowchart TD
          %% STYLE DEFINITIONS
          classDef phase fill:#f2f2f2,stroke:#999,stroke-width:1px,color:#111,font-weight:bold
          classDef system fill:#dbeafe,stroke:#60a5fa,color:#000
          classDef ai fill:#fef3c7,stroke:#facc15,color:#000
          classDef human fill:#dcfce7,stroke:#22c55e,color:#000
          classDef storage fill:#fce7f3,stroke:#ec4899,color:#000
          classDef ledger fill:#ede9fe,stroke:#8b5cf6,color:#000
          classDef governance fill:#fee2e2,stroke:#f87171,color:#000

          %% FLOW
          subgraph P0["0️⃣ Pre-Construction Assurance"]
              A0["Define Assurance Manifest\\n(Design Specs, Codes, Thresholds)"]
              A1["Attestations from Engineers & Suppliers"]
              A2["Baseline Manifest v0.0 Stored in TrustCentre"]
              A0 --> A1 --> A2
          end
          class P0 phase

          subgraph P1["1️⃣ Data Collection & Monitoring"]
              B0["IoT Sensors & Drones\\nCollect QA/QC Data"]
              B1["Material Tests, Safety Logs,\\nEnvironmental Readings"]
              B2["Data Streamed to Assurance Pipeline"]
              B0 --> B1 --> B2
          end
          class P1 system

          subgraph P2["2️⃣ Normalization & Evaluation"]
              C0["Normalize Sensor & Lab Data\\ninto SARIF-like Format"]
              C1["AI Reasoner Compares\\nResults to Manifest Policies"]
              C2{"Threshold Violations?"}
              C0 --> C1 --> C2
          end
          class P2 ai

          subgraph P3["3️⃣ Human-in-the-Loop Review"]
              D0["Inspectors Review AI Flags"]
              D1["Approve, Comment, or Waive Issues"]
              D2["AI Feedback Loop Updated"]
              C2 -->|Yes| D0 --> D1 --> D2
              C2 -->|No| E0
          end
          class P3 human

          subgraph P4["4️⃣ Evidence Generation & Storage"]
              E0["Signed Assurance Report\\n(Cosign / KMS)"]
              E1["Push to TrustCentre OCI"]
              E2["Record in Immutable Ledger\\n(Rekor / QLDB)"]
              D1 --> E0 --> E1 --> E2
          end
          class P4 storage,ledger

          subgraph P5["5️⃣ Continuous Improvement & Audit"]
              F0["Aggregate Historical Data\\ninto Assurance Knowledge Graph"]
              F1["AI Reasoner Updates Thresholds"]
              F2["Regulators & Auditors Verify\\nEvidence and Replay Provenance"]
              F3["Human Committee Reviews\\nPolicy Updates"]
              E2 --> F0 --> F1 --> F2 --> F3
          end
          class P5 governance

          %% EXTERNAL ACTORS
          EXT1(["Design Engineers"])
          EXT2(["Inspectors & Safety Officers"])
          EXT3(["AI Reasoner"])
          EXT4(["TrustCentre / Ledger"])
          EXT5(["Regulators / Auditors"])

          %% RELATIONSHIPS
          EXT1 -.-> A0
          EXT2 -.-> D0
          EXT3 -.-> C1
          EXT4 -.-> E1
          EXT5 -.-> F2

          %% FLOW ORDER
          P0 --> P1 --> P2 --> P3 --> P4 --> P5
    ```

## 0) Pre-Construction Assurance (Design & Planning)

* Validate that the **Assurance Manifest** matches design specifications and applicable regulatory codes (CSA, ASTM, ISO 9001).  
* Require signed attestations from design engineers, material suppliers, and site supervisors before construction begins.  
* Run simulated probes in BIM or digital twin environments to verify that thresholds and sensor configurations are adequate.  
* Store this as a **baseline manifest** (`v0.0`) in the TrustCentre for future comparison and drift detection.  

---

## 1) Goal

Continuously test and verify that construction projects, materials, and subcontractor processes remain **safe, compliant, and within performance thresholds**, producing *signed, immutable evidence* of quality at every phase.

---

## 2) Key Actors

| Role | Construction Equivalent | Function |
| ----- | ----- | ----- |
| **System Under Test (SUT)** | Project Site, Structural Section, or Subsystem (HVAC, electrical, concrete pour) | The entity being verified |
| **Assurance Pipeline** | Construction QA/QC Process + Digital Twin Sensors | Continuous data collection and analysis |
| **TrustCentre** | Immutable Evidence Repository (Digital Twin Ledger / BIM Integration) | Stores signed reports, inspections, IoT telemetry |
| **AI Reasoner** | Predictive Analytics / Compliance Checker | Interprets data, identifies anomalies, and verifies specs |
| **Auditors / Inspectors** | Building Officials, Safety Officers, Owner’s Reps | Verify evidence and sign off on assurance milestones |

---

## 3) Assurance Manifest (Construction Equivalent)

Defines *what makes the project compliant and safe.*

```yaml
project:
  id: "highway-bridge-segment-42"
  version: "2025.10.10"
  phase: "Foundation Pour"
  contractor: "Steel & Stone Ltd."
  evaluation:
    schedule: "R/1D"                 # daily checks
    datasets:
      - name: "ConcreteSampleTests"
        kind: "material_quality"
        source: "oci://trustcentre/lab/concrete_results@sha256:..."
      - name: "WorkerSafetyIncidents"
        kind: "safety"
        source: "oci://trustcentre/site/safety@sha256:..."
      - name: "EnvironmentalSensors"
        kind: "environmental"
        source: "oci://trustcentre/iot/environment@sha256:..."
    metrics:
      material_quality:
        compressive_strength_min: 30  # MPa
        slump_range: [75, 125]        # mm
      safety:
        lost_time_injuries: 0
        near_miss_rate_max: 0.02
      environmental:
        vibration_threshold_mm_s: 10
        noise_threshold_db: 85
    gates:
      block_on_fail: ["safety", "material_quality"]
      warn_on_fail: ["environmental"]
    attestations:
      sign_with: "cosign://keys/qa-inspector"
      publish_to: "oci://trustcentre/org/assurance"
```

---

## 4) Assurance Flow

??? info "Assurance Flow (Safe Sequence)"
    ```mermaid
    sequenceDiagram
        participant SUT as System_Under_Test
        participant TC as TrustCentre
        participant PROBE as Scheduled_Probes
        participant NORM as Normalization
        participant AI as AI_Reasoner
        participant HUM as Human_Review
        participant GATE as Sign_Publish_Service
        participant LED as Audit_Ledger
        autonumber

        %% Registration
        SUT->>TC: Register subsystem or site with identifiers and certificates
        TC-->>SUT: Return registration record

        %% Data Collection
        PROBE->>SUT: Collect daily readings for strength, temperature, humidity, and vibration
        SUT-->>PROBE: Provide sensor data, safety logs, equipment telemetry, and environment values
        PROBE->>NORM: Send collected data for processing

        %% Normalization
        NORM->>NORM: Convert results into standard format
        NORM->>NORM: Remove duplicates and anomalies for consistency

        %% Evaluation
        NORM->>AI: Provide normalized dataset for analysis
        AI->>AI: Compare values against manifest thresholds and detect drift
        AI->>AI: Check strength threshold satisfied then mark as pass
        AI->>AI: Check for safety incidents then mark as block
        AI->>AI: Check noise level greater than eighty five decibel then mark as warn
        AI-->>HUM: Send flagged anomalies for review

        %% Human Review
        HUM->>HUM: Review anomalies and add comments or waivers or retest plans
        HUM-->>AI: Return human decisions and notes

        %% Signed Assurance Report
        AI->>GATE: Create final assurance report with material, safety, and environmental results
        GATE->>GATE: Sign report using trusted signing service
        GATE->>TC: Upload signed report to immutable registry
        GATE->>LED: Log digest signer timestamp and metadata
        GATE-->>SUT: Provide access reference for report
    ```



### 4.1 Registration

* Each subsystem or site section is registered as a “System Under Test” in TrustCentre.  
* Includes BIM element IDs, geographic coordinates, and subcontractor certifications.

### 4.2 Data Collection (Scheduled Probes)

* IoT sensors, drones, and lab data streams feed **daily probes**:  
  - Concrete strength tests, curing temperature, humidity, vibration levels.  
  - Safety incident logs, worker attendance, equipment telemetry.  
  - Environmental readings (noise, dust, CO₂).

### 4.3 Normalization

* All sensor and lab results normalized into a **SARIF-like format**.  
* Deduplication and anomaly removal for consistent evaluation.

### 4.4 Evaluation (Policy Comparison)

* **AI Reasoner** compares results to Manifest thresholds and detects drift trends.  
* Example checks:  
  - Compressive strength ≥ 30 MPa → Pass  
  - Any safety incident → Block  
  - Noise > 85 dB → Warn  

### 4.5 Human-in-the-Loop Review

* Engineers or inspectors review AI-flagged anomalies.  
* Add comments or waivers (“allowed due to environmental conditions; retest scheduled”).  

### 4.6 Signed Construction Assurance Report

* Aggregates results + human notes into a report that includes:  
  - Material, safety, and environmental compliance.  
* Signed (Cosign/KMS) and pushed to TrustCentre OCI.  
* Logged in the Audit Ledger for non-repudiation.

---

## 5) Example Report (Summary JSON)

```json
{
  "report_id": "bridge-42-foundation-2025-10-10",
  "section": "Foundation Pour",
  "metrics": {
    "material_quality": { "strength_mpa": 32.4, "slump_mm": 102, "status": "pass" },
    "safety": { "lt_injuries": 0, "near_miss_rate": 0.01, "status": "pass" },
    "environmental": { "noise_db": 88, "status": "warn" }
  },
  "policy_verdict": { "overall": "pass_with_warning" },
  "signed_by": "qa_inspector@steelandstone.com",
  "rekor_log_id": "log:8832",
  "timestamp": "2025-10-10T19:45:00Z"
}
```

---

## 6) AI Reasoner Extensions for Construction

| Function | Example Behavior |
| ----- | ----- |
| **Drift Detection** | Detects sensor degradation or slump deviation suggesting supply issues. |
| **Predictive Risk Forecasting** | Predicts curing temperature violations or schedule slippage. |
| **Fairness / Safety Analytics** | Ensures equal safety compliance across subcontractors or shifts. |
| **Policy Alignment Reasoning** | Maps construction spec IDs to manifest rules automatically. |
| **Explainable Root Cause Reports** | “Slump deviation due to humidity 80%; suggest admixture adjustment.” |

---

## 7) Digital Evidence Chain

Every result — lab report, safety log, AI analysis, human approval — is:

* **Signed & Timestamped:** via Sigstore or PKI.  
* **Anchored in Rekor:** for public verifiability.  
* **Stored in WORM OCI:** immutable evidence retention.  
* **Linked to BIM / Digital Twin:** each element references its evidence bundle.  
* **Auditable:** regulators can replay the full evidence chain.

---

## 8) Integration Opportunities

| Layer | Example Integration |
| ----- | ----- |
| **Pipeline (Data Ingestion)** | Autodesk Construction Cloud, Procore, Trimble Connect, IoT gateways |
| **AI Reasoner** | Custom models for safety risk prediction and material drift |
| **TrustCentre / Ledger** | immudb or AWS QLDB for immutable ledger, OCI registry for evidence |
| **Visualization** | PowerBI / Grafana overlayed on BIM model |
| **Notifications** | Slack/Teams integration for compliance alerts |
| **Regulatory Compliance** | Integration with building codes, ISO QA records, OSHA/CSA safety compliance |
| **Governance & Audit** | Export of assurance evidence to shared TrustCentre nodes for regulators |

---

## 9) Benefits to the Construction Industry

| Benefit | Description |
| ----- | ----- |
| **Continuous Assurance** | Real-time verification of quality & safety, not just milestone inspections. |
| **Immutable Evidence** | Digitally signed, tamper-proof QA/QC records. |
| **Predictive Prevention** | Detect drift or degradation early to prevent rework. |
| **Trust Exchange** | Share verifiable assurance data with owners and regulators. |
| **Cross-Project Intelligence** | AI Reasoner learns patterns across projects to optimize QA/QC policies. |

---

## 10) Future Extensions

1. **Supply Chain Material Assurance**  
   * Link suppliers (cement, steel) to TrustCentre; every batch has a signed material manifest.  
   * Enables ESG provenance tracking.  

2. **AI + Digital Twin Synchronization**  
   * Connect assurance reports to live digital twin models; visualize compliance zones in 3D.  

3. **Zero-Trust Construction Certification**  
   * Regulators verify compliance proofs directly in TrustCentre instead of manual logs.  

4. **Predictive Maintenance Integration**  
   * Extend assurance to post-construction operational monitoring (bridges, HVAC, etc.).  

---

## 11) Continuous Improvement Loop

* Each project contributes anonymized data back into a shared Assurance Knowledge Graph.  
* The AI Reasoner updates manifest thresholds and risk models using historical trends.  
* Policy recommendations are proposed for review and signed into new manifest versions.  
* Human review committees ensure that automation remains explainable and auditable.  

---

## 12) Testing & Validation Framework

* **Simulation Tests:** Verify sensors and manifest logic trigger correctly under test conditions.  
* **Drift Calibration Tests:** Confirm sensor accuracy and calibration integrity.  
* **Human-in-the-Loop Testing:** Random audits of AI-flagged events with dual approval.  
* **End-to-End Replay:** Re-run historical data to ensure deterministic reproducibility.  

---

## Summary

The same “Continuous Assurance” framework that validates AI systems can validate physical systems like bridges or buildings.  
The only change is the *domain of metrics* — but the principles of traceability, immutability, and verifiable policy alignment remain identical.

---
