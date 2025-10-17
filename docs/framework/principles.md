# Principles

This platform is intentionally designed as a framework, specification, and ontology not a prescriptive product. Its purpose is to define how assurance should work, not what specific tools must be used.  We hope to provide a common language and structure for continuous assurance.  If you will, a modular blueprint that any organization can implement using the technologies and processes that best fit their environment.

## Benefits of the this Approach

* **Flexibility:** Teams can start small and expand. You can swap scanners, ticketing systems, or registries without redesigning the pipeline underlying the platform.
* **Portability:** Works across cloud, on-prem, or hybrid environments.
* **Interoperability:** Encourages open standards (SARIF, OCI, SLSA, JSON-LD) instead of proprietary formats.
* **Governance Consistency:** Even if implementations differ, the assurance model and ontology stays the same.
* **Future-Proofing:** As new tools emerge, they can be plugged into existing stages with minimal friction.

Here is what we encourage everyone to keep in mind as we build this out:

---

## **Core Principles**

## 1. Start with the Evidence, Not the Ticket

Your Ticket Management System (TMS) is not your source of truth. It is your collaboration layer. The source of truth is the immutable evidence store:

- Each scanner (OpenGrep, ZAP, Trivy, Grype, etc.) outputs a signed SARIF or JSON file.  
- Those files are stored in an OCI registry or a WORM-locked bucket, indexed by content digest.  
- You can always reproduce, verify, and attest to what was found, when, and by which tool.  
- Your ticket system should simply reference that evidence, rather than owning it.  

**Example:**  
`Evidence: ghcr.io/org/app@sha256:abcd.../trivy.sarif`  

That single line in every ticket creates an unbreakable chain of custody between your human process and the machine-verifiable proof.

---

## 2. Normalize Once, Then Reuse Everywhere

Most scanners already support **SARIF**, the industry’s lingua franca for security results.  
Instead of letting each scanner speak its own dialect, build a tiny “normalizer” job in your CI pipeline that:

- Merges and enriches results with metadata (commit, image digest, branch, environment).  
- Computes deterministic fingerprints so findings can be de-duplicated across runs.  
- Emits a canonical JSON structure that any downstream system — Jira, Grafana, or a data lake — can consume.  
{u6t5r4ewdsqa    dfergt
|?qx    }
This is your **Normalization Layer.**  
Everything above it (TMS, dashboards, metrics) is replaceable.  
Everything below it (SARIF, SBOMs, attestations) is immutable.

---

## 3. Replace the “Platform” With a Simple Sync Layer

Once you’ve normalized your data, the integration with a TMS is surprisingly straightforward.

Your CI/CD pipeline (or a scheduled job) can:

1. Parse the normalized SARIF.  
2. Derive a unique key per finding (e.g., ruleId + location).  
3. Query your workload/ticket system for an issue with that key in a custom field or label.  
4. Create or update the issue as needed.  

**Example pseudo-logic:**

```python
fingerprint = sha1(ruleId + file + line)
if not tms.issue_exists(fingerprint):
    tms.create_issue(summary, description, severity, fingerprint)
else:
    tms.comment_issue(fingerprint, "Still open in commit abc123")
```

This approach gives you the same automation you’d expect from a dedicated vulnerability platform — but using lightweight code you control.

You can even enrich issues with contextual metadata:

- **Severity:** mapped from the scanner  
- **Component or Service:** derived from repository or manifest  
- **Commit / PR:** for developer traceability  
- **Evidence URI:** immutable link to SARIF  
- **Labels:** `tool:semgrep`, `env:prod`, `scanner:v1.8`  

Now your TMS becomes a thin, flexible interface for triage and remediation — not a bloated database.

---

## 4. Keep It Immutable, even in Your TMS

Immutability doesn’t stop at storage. It’s a philosophy that should extend all the way to your workflow.

Here’s how to preserve that principle:

- Don’t edit findings manually. Update them only by re-running scans and re-syncing.  
- Never overwrite. If a vulnerability reappears, record it as a state change (“re-opened”), not a fresh ticket.  
- Keep digests, not filenames. References like `sha256:abcd...` ensure long-term verifiability.  
- Sign your syncs. Each batch of issues you create or update can be wrapped in an attestation (`cosign attest`) listing input digests and timestamps.  

This turns a process like vulnerability management into a cryptographically provable audit trail, not a spreadsheet of opinions.

---

## 5. Automate the Metrics

Once you decouple your evidence from your workflow, metrics become trivial.

A small exporter service can query your ticket system (or your normalized JSON store) and expose metrics such as:

```
security_findings_open{severity="critical"} 3
security_findings_sla_breached_total 1
security_last_scan_timestamp_seconds{repo="api"} 1696883400
```

A tool like Grafana can then visualize:

- Open findings by severity or service  
- Mean Time to Remediate (MTTR)  
- Aging of unresolved issues  
- Scan freshness per environment  

These metrics drive your conversations — not subjective dashboards or PDF reports.

---

## 6. Enforce Policy as Code

With everything in Git and everything signed, policy enforcement becomes a simple gate.  

Use **CUE** or **OPA** to define your security SLOs:

- “Critical findings = 0”  
- “No unresolved Highs older than 30 days”  
- “Last scan < 24h old”  

Evaluate those policies in CI/CD before promotion.  
Fail the deployment if any condition isn’t met.  

That’s real continuous assurance — automated, auditable, and provable.

---


## 7. Design for Verifiability, Not Just Visibility

Dashboards show what’s happening; verifiability proves it.  
Every output should be independently checkable  by an auditor, a developer, or an external regulator.

- Prefer **signed attestations** to unsigned reports.  
- Record **policy version**, **scanner version**, and **timestamp** for every result.  
- Enable **re-verification** (replayability) from artifact digests without re-running scans.  

This ensures your assurance pipeline produces *proofs*, not just *reports*.

---

## 8. Embrace Composability Over Centralization

Avoid the “single platform” trap — your ecosystem should be **modular**, not monolithic.

- Build around **open standards** (SARIF, SPDX, OPA, OCI).  
- Each component (scanner, normalizer, sync, visualizer) can be swapped without breaking the chain.  
- Encourage integration through **API contracts** and **schema stability**, not through UI lock-in.  

This philosophy keeps your system resilient to vendor churn and technological drift.

---

## 9. Treat Assurance as a Feedback Loop

Assurance isn’t a one-way audit; it’s a continuous learning cycle.

- Feed enriched metrics (MTTR, false-positive rate, bias score, etc.) back into scanner configuration and policy thresholds.  
- Automatically tune or retire ineffective rules.  
- Use AI-based triage or clustering to guide developers toward *high-value* remediations first.  

The goal is adaptive improvement, not static compliance.

---

## 10. Build for Multi-Persona Collaboration

Different participants — developers, auditors, data scientists, compliance officers — all view “risk” differently.

- Deliver **context-appropriate surfaces** (e.g., Jira tickets for devs, Grafana dashboards for ops, signed ledgers for auditors).  
- Maintain a **shared vocabulary** through consistent metadata fields (e.g., `severity`, `component`, `evidence_uri`).  
- Keep human review paths explicit, auditable, and reversible.  

This avoids the common trap of one-size-fits-all “security portals.”

---

## 11. Assume Policy, Tooling, and AI Drift

Every system you integrate — scanners, AI validators, even governance policies — will evolve.  
Design for graceful drift rather than brittle coupling.

- Version everything (schemas, policies, prompts, AI models).  
- Persist results *with* their context (model hash, policy commit, scanner version).  
- Periodically **re-evaluate historical evidence** with updated logic to maintain continuity across generations.  

This provides temporal assurance: what was compliant last year can still be verified under today’s rules.

---

## 12. Make AI Optional, Keep Humans in the Loop

AI can accelerate assurance — summarizing evidence, highlighting anomalies, or assisting triage — but it must never become a single point of failure or authority.  

- Treat AI as a **copilot**, not a **controller**. Its role is to *augment* human judgment, not replace it.  
- Every AI output (classification, reasoning trace, recommendation) should be **traceable**, **explainable**, and **verifiable** via supporting evidence.  
- Always allow human override — approvals, waivers, and sign-offs must remain cryptographically attributable to a person or policy key.  
- Build the pipeline so that **AI components can be disabled entirely** without breaking the assurance flow.  
- Log both **AI prompts** and **responses** in your immutable evidence store for accountability and reproducibility.  

The goal isn’t to make assurance “autonomous”; it’s to make it **assistive, auditable, and reversible** — where every automated insight still ends in a human’s informed decision.

---
