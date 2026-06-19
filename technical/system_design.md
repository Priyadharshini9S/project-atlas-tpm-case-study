# Technical Fluency & Architecture Breakdown

### 1. Idempotency & Unique Event Identifiers
* [cite_start]**Why do we require a client-generated `event_id`?** It allows the ingestion engine to distinguish between an intentional separate charge and a duplicate network retry[cite: 20]. [cite_start]Without it, network retries will result in over-billing, incorrectly depleting the customer's wallet balance[cite: 20].
* [cite_start]**Is a retry safe?** Yes, but *only* if the endpoint checks whether the unique `event_id` has already been processed before mutating balances[cite: 20]. [cite_start]If the event exists, it drops the duplicate and returns a cached success response[cite: 20].

### 2. Distributed Webhook Mechanics
* **Why do at-least-once webhooks duplicate events?** In a distributed system, network unreliability prevents the sender from knowing whether a payload failed to arrive, or if it arrived successfully but the acknowledgment dropped[cite: 20]. To avoid missing critical alerts (e.g., low-balance webhooks), the system defaults to retrying delivery[cite: 20]. Handlers must be engineered to be completely idempotent (e.g., executing `SET status = 'low'` instead of a stateful step like `wallet_balance -= 10`)[cite: 20].
* **Debugging Webhook Delivery Failures (First 3 Triages):**
  1. [cite_start]**Source Validation:** Check our delivery logs to ensure the event infrastructure successfully triggered when the threshold was crossed[cite: 20].
  2. [cite_start]**Ingress Evaluation:** Review the exact HTTP response codes returned by the receiver's endpoint (e.g., 504 Timeouts, 403 TLS handshake errors, 400 Bad Requests)[cite: 20].
  3. [cite_start]**Network & Security Layer Verification:** Inspect intermediate firewalls, IP blocklists, malformed target URLs, or silent signature authentication failures[cite: 20].

### 3. Merchant of Record (MoR) Model
[cite_start]When Dodo acts as the **Merchant of Record (MoR)**, it assumes full financial, regulatory, and legal liability for selling to the end consumer[cite: 20]. 
* This means computing local sales taxes, managing cross-border processing laws, and managing compliance remittance across all 190+ markets is entirely Dodo's responsibility, not Northwind's[cite: 20]. 
* [cite_start]A failure to launch accurate localization metrics directly exposes Dodo to significant auditing and compliance risk[cite: 20].

---
🗂️ [Back to Main Project Summary](../README.md)
