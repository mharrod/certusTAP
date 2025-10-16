# The TAO of CertusTAP

### A Semantic Framework for Machine-Verifiable Trust


## Purpose

The **Trust & Assurance Ontology (TAO)** defines a **unified language for trust** — enabling systems, auditors, and AI agents to *understand, reason about, and prove* that digital systems are secure, ethical, and trustworthy.

> **TAO makes assurance explainable and verifiable — by both humans and machines.**

It captures how trust is built, evidenced, and validated across the assurance lifecycle using cryptographic, analytical, and procedural evidence.

---

## Core Idea

Modern organizations generate vast amounts of assurance evidence — vulnerability scans, AI model evaluations, provenance logs, attestations, and risk assessments.  
However, these artifacts are often siloed, inconsistent, and lack a shared structure.

**TAO’s goal** is to connect them in a semantic knowledge graph that can answer questions like:

- “What evidence supports this claim of fairness or compliance?”  
- “Who signed this attestation and when?”  
- “Which policies does this artifact satisfy?”  
- “Can we prove this model’s provenance and ethical compliance?”

---

## Ontology Summary

TAO models the **entities**, **artifacts**, **evidence**, and **relationships** that define digital trust.

| Concept | Description | Example |
|----------|--------------|----------|
| **Entity** | Actor, person, or organization producing or signing artifacts. | ACME Labs, Independent Auditor |
| **Artifact** | Object under assurance (software, model, dataset, report). | Model v2.0, Build Artifact |
| **Evidence** | Proof supporting a claim. | Bias Test Report, Security Scan Results |
| **Claim** | Statement requiring evidence. | “Model is bias-mitigated.” |
| **Policy / Control** | Normative rule or standard requirement. | ISO 27001, NIST 800-53, AI Fairness Policy |
| **Evaluation** | Assessment producing metrics or scores. | DeepEval run, Privacy Scan |
| **Assurance Case** | Structured argument linking claims and evidence. | AI Model Assurance Case |
| **Trust Proof** | Verifiable output (attestation, signed proof). | Cosign signature, RFC3161 timestamp |
| **Risk** | Identified issue with impact and mitigation. | PII exposure risk |
| **Signature / Timestamp** | Cryptographic verification artifacts. | Cosign keyless sig, TSA token |

Together, these form a **semantic web of assurance** — every artifact is traceable to its origin, evidence, and proof.

---

## Role Within Certus TAP

| Goal | How TAO Enables It |
|------|--------------------|
| **Continuous Assurance** | Every scan, evaluation, or attestation becomes a trust node in the ontology. |
| **Traceability & Provenance** | Each artifact links to its producer, build, and assurance evidence. |
| **Automation & Reasoning** | Ontology rules enable inference of compliance or detection of missing evidence. |
| **Explainability & Auditability** | Auditors can see *why* something is trusted through linked evidence and claims. |
| **Integration** | Harmonizes Haystack, Presidio, DeepEval, and Cosign outputs under one schema. |
| **Governance & Interoperability** | Aligns with W3C PROV-O, ISO 15026, and NIST OSCAL standards. |

---

## How It Works

1. **Evidence Generation**  
   Tools like Presidio, DeepEval, and scanners produce structured results.  
   → Annotated as `tao:Evidence`, `tao:Evaluation`, or `tao:Risk`.

2. **Semantic Linking**  
   Each artifact, claim, and policy is connected using ontology relationships:

   ```
   Artifact → hasEvidence → Evidence → supportsClaim → Claim → satisfiesPolicy → Policy
   ```

3. **Reasoning & Validation**  
   Using OWL or SHACL logic, TAO enables reasoning such as:  
   *“If an artifact’s evidence supports a claim that satisfies a policy, then that artifact is compliant.”*

4. **Publication & Verification**  
   Results (trust proofs, attestations) are signed and published to a WORM OCI registry — forming immutable assurance records.

5. **Visualization & Querying**  
   The trust graph can be queried via SPARQL or visualized in dashboards to demonstrate compliance and provenance.

---

## Integration Examples

| System | Ontology Role |
|---------|----------------|
| **Haystack 2** | Provides the reasoning and RAG interface; each document is semantically annotated using TAO. |
| **Presidio** | Generates privacy assurance evidence (`tao:Evidence`). |
| **DeepEval** | Produces evaluation metrics (`tao:Evaluation` with `scoreType` and `scoreValue`). |
| **Cosign / Rekor** | Creates `tao:TrustProof`, `tao:Signature`, and `tao:Timestamp` for cryptographic verification. |
| **OCI Evidence Registry** | Stores verifiable assurance cases and attestations. |

---

## Why It Matters

- **Trust becomes measurable** — every claim is backed by structured evidence.  
- **AI becomes explainable** — assurance lineage is visible end-to-end.  
- **Security becomes provable** — provenance and signatures are verifiable.  
- **Compliance becomes continuous** — validation happens automatically.  
- **Collaboration becomes semantic** — humans and machines share a common assurance language.

---

## The Vision

> TAO transforms assurance from a static audit into a living knowledge graph of trust —  
> where every artifact carries its own verifiable provenance and proof of integrity.

It forms the foundation of a machine-understandable Trust Center, where evidence, reasoning, and signatures converge to power Continuous, Explainable Assurance.

---

**Author:** TAO Working Draft  
**Maintainer:** Martin Harrod  
**License:** Creative Commons Attribution 4.0 International (CC-BY 4.0)  
