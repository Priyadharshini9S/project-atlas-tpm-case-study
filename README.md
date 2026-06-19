# project-atlas-tpm-case-study
├── README.md               # The main landing page / Case study write-up
├── documents/
│   ├── risk_register.md    # Formatted Risk & Dependency Register
│   └── process_cadence.md  # Process & Ways of Working strategy
└── technical/
    └── system_design.md    # Technical Fluency & API Architecture analysis

# Project Atlas: Usage-Based Billing for Northwind (TPM Case Study)

An end-to-end Technical Project Management portfolio demonstrating milestone planning, cross-functional risk mitigation, critical path analysis, and technical governance for a highly complex financial infrastructure initiative.

## 🚀 Project Overview
* **Objective:** Launch usage-based billing infrastructure for a major enterprise client (Northwind) before their major keynote event.
* **Timeline:** Strict 8-week delivery path with zero slack.
* **Target Architecture:** Scalable metering ingestion, real-time credit wallet depletion, low-balance webhooks, regionalized invoicing, and Merchant of Record (MoR) automated tax compliance.

---

## 🗺️ 8-Week Milestone Plan & Critical Path Analysis
The critical path is driven entirely by downstream engineering dependencies. Below is the structured delivery schedule mapped across functional teams:

| Workstream | Owner | Critical Path? | Week 1-4 Execution | Week 5-8 Execution | Key Milestones & Notes |
| :--- | :--- | :---: | :--- | :--- | :--- |
| **API Contract & Event Schema** | TPM Platform Billing | **YES** | Finalize & Freeze (W1) | *Completed* | Schema frozen & shared with Northwind by end of W1. Everything downstream blocks on this. |
| **Metering Ingestion** | Platform Team | **YES** | Design (W1) $\rightarrow$ Build (W2-W3) $\rightarrow$ Unit Test & Merge (W4) | *Completed* | Platform operating at ~60% capacity due to parallel DB migration. Strict deadline to avoid blocking invoicing. |
| **Credit Balance & Wallet** | Billing Team | **YES** | Design (W1) $\rightarrow$ Build (W2-W3) $\rightarrow$ Integrate (W4) | *Completed* | Built in parallel with metering. Depletion logic integrated and tested by end of W4. |
| **Invoicing Engine** | Billing Team | **YES** | *Blocked* | Build (W5-W6) $\rightarrow$ Integration Test (W7) $\rightarrow$ Go-Live (W8) | Gated by Metering + Wallet. Built with reduced capacity (2 of 3 engineers) in W5-W6. High risk. |
| **Payments & MoR** | Payments Compliance | **YES** | Request External Flag (W1) $\rightarrow$ Await Flag | Integrate Partner Flag (W5) $\rightarrow$ Tax Config Test (W6) $\rightarrow$ Finalize (W7) | Gated entirely by external partner flag landing and localized Tax sign-offs. |
| **Developer Experience** | DevRel Platform | No | Draft Docs (W1) $\rightarrow$ SDK Build (W2-W3) $\rightarrow$ Beta SDK (W4) | Finalize via Feedback (W5) | Crucial for Northwind's integration timeline. Beta SDK in their hands by W5 gives them 3 weeks of runway. |
| **E2E Testing & UAT** | Cross-functional | **YES** | *Blocked* | E2E Internal Testing (W6) $\rightarrow$ Northwind UAT & Fixes (W7) | First point all components run together. Any late bugs have only 1 week of slack before go-live. |

---

## 🧠 The Hard Call: Executive Trade-Off (End of Week 3 Crisis)

### The Situation
At the end of Week 3, a dual-blocker materializes:
1. The external payment partner has gone completely silent on the required pricing flag.
2. An internal database migration overran, putting the Platform team behind on the core metering engine.

### The Decision (Minimum Launchable Scope)
Rather than blindly delaying the launch or making false promises regarding the 8-week timeline, I made the call to **strategically descope the launch** to protect the Keynote date while preserving operational correctness:

* 🚫 **What I Cut/Deferred:** * Descoped day-one global launch across 190+ countries. Reduced to top-revenue markets where existing rails and tax configurations already work. 
  * Deferred edge-case automated invoicing (credit notes, manual adjustments) to manual operations for cycle one.
  * Deferred strict real-time hourly SLA; accepted near-real-time aggregation for the initial invoicing cycle to ease Platform engineering pressure.
* 🛡️ **What I De-Risked:** * Escalated the payment partner silence to the executive relationship owner immediately.
  * Narrowed the compliance team's scope to guarantee absolute legal tax sign-off for the reduced country list on day one.
* 🤝 **The Plainly Stated Trade-Off:**
  * We traded a synchronized global cutover for a highly reliable, functioning launch in core markets on Keynote day. The Northwind CEO can truthfully claim usage-based billing is live; remaining countries will be phased in as a fast-follow within 2-3 weeks.

---

## 📂 Additional Artifacts

* Detailed Risk Register: [documents/risk_register.md](documents/risk_register.md)
* Technical & Architectural Analysis: [technical/system_design.md](technical/system_design.md)
* Agile Process & Cadence Design: [documents/process_cadence.md](documents/process_cadence.md)

# Risk & Dependency Register

Ranked by combined likelihood and impact. Reviewed weekly by cross-functional stakeholders.

| # | Risk / Dependency | Likelihood & Impact | Early Signal / Metric to Watch | Mitigation / Plan B | Owner |
| :-: | :--- | :---: | :--- | :--- | :--- |
| **1** | Payment partner pricing flag slips past Week 5. | **Med / High** | No written ETA or status reply from partner by end of W2. | Escalate to relationship owner at W2. Pre-build a fallback manual/flat residual charge path for launch day to bypass vendor dependency. | TPM + Payments Lead |
| **2** | Billing's 1-engineer leave (W5-W6) collides with the complex Invoicing build. | **High / High** | Invoicing design/spec isn't fully locked by end of Week 4. | Front-load design reviews into W3-W4 before the engineer leaves so W5-W6 is pure execution. Scope code to MVP reconciliation only. | TPM + Billing Lead |
| **3** | DB migration overruns past Week 3, further delaying metering ingestion. | **Med / High** | Migration status check-in at end of W2 reveals progress is <80%. | Secure a hard cutover commitment in W1. If it slips, pull an engineer off migration support to protect the metering deadline. | TPM + Platform Lead |
| **4** | Tax sign-off incomplete or delayed across 190+ countries. | **Med / High** | Tax compliance team hasn't produced a scoped country list/risk tiering by end of W2. | Narrow initial launch to countries covered by existing Merchant of Record (MoR) rules. Sequence rest as post-launch fast-follows. | TPM + Tax Lead |
| **5** | Metering/wallet integration surfaces bugs late (double-counting, dropped events). | **Med / Med-High** | Reconciliation discrepancies appear during W4 integration tests. | Build an automated reconciliation/audit report into metering from day one so mismatches are visible in W4 instead of deep in W6 invoicing. | Platform Lead |
| **6** | Northwind's integration lags because Dodo docs/SDK arrive too late. | **Med / Med** | Northwind has not kicked off their integration sprint by Week 5. | Ship a docs/SDK "preview" by end of W2 based on the frozen contract schema, allowing parallel client integration. | DevRel Lead |
| **7** | Unclear ownership at the metering/wallet handoff between Platform and Billing. | **Med / Med** | Conflicting assumptions surface in standups regarding deduplication/depletion logic ownership. | Author and sign-off on a one-page interface contract (event schema, dedupe ownership, depletion triggers) in Week 1. | TPM |



# Technical Fluency & Architecture Breakdown

### 1. Idempotency & Unique Event Identifiers
* **Why do we require a client-generated `event_id`?** It allows the ingestion engine to distinguish between an intentional separate charge and a duplicate network retry. Without it, network retries will result in over-billing, incorrectly depleting the customer's wallet balance.
* **Is a retry safe?** Yes, but *only* if the endpoint checks whether the unique `event_id` has already been processed before mutating balances. If the event exists, it drops the duplicate and returns a cached success response.

### 2. Distributed Webhook Mechanics
* **Why do at-least-once webhooks duplicate events?** In a distributed system, network unreliability prevents the sender from knowing whether a payload failed to arrive, or if it arrived successfully but the acknowledgment dropped. To avoid missing critical alerts (e.g., low-balance webhooks), the system defaults to retrying delivery. Handlers must be engineered to be completely idempotent (e.g., executing `SET status = 'low'` instead of a stateful step like `wallet_balance -= 10`).
* **Debugging Webhook Delivery Failures (First 3 Triages):**
  1. **Source Validation:** Check our delivery logs to ensure the event infrastructure successfully triggered when the threshold was crossed.
  2. **Ingress Evaluation:** Review the exact HTTP response codes returned by the receiver's endpoint (e.g., 504 Timeouts, 403 TLS handshake errors, 400 Bad Requests).
  3. **Network & Security Layer Verification:** Inspect intermediate firewalls, IP blocklists, malformed target URLs, or silent signature authentication failures.

### 3. Merchant of Record (MoR) Model
When Dodo acts as the **Merchant of Record (MoR)**, it assumes full financial, regulatory, and legal liability for selling to the end consumer. 
* This means computing local sales taxes, managing cross-border processing laws, and managing compliance remittance across all 190+ markets is entirely Dodo's responsibility, not Northwind's. 
* A failure to launch accurate localization metrics directly exposes Dodo to significant auditing and compliance risk.


# TPM Operating Cadence & Process Improvement Strategy

## 📉 Diagnosing Process Pitfalls: The Cost of "Invisible Risks"
In complex systems, risks often develop silently over weeks before turning into combined delivery crises. If a risk is only called out when a milestone officially slips, the team loses the valuable time buffer where mitigations are cheap to implement.

## 🛠️ The Proposed Change: Asynchronous Telemetry
To maximize engineering velocity and remove status-meeting overhead, the following operational structure is established:

### Operational Rituals to Maintain:
* **Async Daily Standup:** 2-3 bullet lines per engineer via slack/tooling. No disruptive live calls.
* **Targeted Cross-Team Syncs:** A 30-minute live working session held *only* between specific teams whose technical dependencies are due that week.
* **Standing External Dependency Audits:** A dedicated, weekly verification check for external vendors (Partner flags, Tax). Logging "No Update" two weeks in a row acts as an automatic trigger for high-level management escalation.

### Rituals to Eliminate:
* 🚫 Cross-functional full-room status reporting syncs (reading out text line-by-line that is already written).
* 🚫 Process reviews and retrospectives scheduled during critical crunch/delivery weeks.

## 📈 Metric of Success
We evaluate the performance of this operational shift by tracking the **Callout Lag**. This measures the duration of time between when an issue began trending towards "Amber" in retrospect and when it was openly communicated. Success is defined as this lag trending down to zero over a four-week period.

* Detailed Risk Register: [documents/risk_register.md](documents/risk_register.md)
* Technical & Architectural Analysis: [technical/system_design.md](technical/system_design.md)
* Agile Process & Cadence Design: [documents/process_cadence.md](documents/process_cadence.md)
