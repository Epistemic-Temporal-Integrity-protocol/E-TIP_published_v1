# Main protocol is live: https://epistemic.center 

This serves as a backup

## Epistemic Temporal Integrity Protocol (ETIP) v1.2
*Drafted by Y. Chen | https://www.linkedin.com/in/yipingchensg/*

**DISCLAIMER**: This protocol is provided “as is” without warranty of any kind. ETIP is a tool for history preservation; it does not validate the truth, accuracy, or legality of the data being hashed. The author shall not be liable for any claims or damages arising from the use of this protocol.

---

### 1. Positioning & Intent

ETIP exists to solve a simple, stubborn problem: data can be quietly rewritten.  
In systems like ESG reporting, numbers do not just exist — they evolve. What is often lost is not the latest value, but the **history** of how that value came to be.

> *“What did you say before — and did you change it later?”*

ETIP is designed so that this question always has an answer. It does not try to prove truth, judge correctness, or enforce honesty. Instead, it does something narrower, but more reliable:  
**It makes it impossible to rewrite the past without leaving evidence.**

Do not describe ETIP using market language (e.g., “token”, “mint”).  
Do not treat ETIP as an asset system.  
Do not treat ETIP as verification of “truthfulness” of content.  
Do not treat ETIP as a consensus mechanism or blockchain.

---

### 2. Design Positioning

ETIP is a post‑blockchain system, and also post‑heavy infrastructure. It keeps what mattered — chaining, fingerprinting, external witnessing — and removes what didn’t: consensus overhead, global coordination, and energy‑intensive operation.  

ETIP takes what worked, and removes what didn’t.  

It is not designed for consensus, markets, or scale at any cost. It is designed for one thing only: **preserving the temporal integrity of records**.

---

**3. Historical Lineage & Prior Art**

ETIP stands on existing ideas rather than replacing them:

- **Hash chaining** — linking records so the past cannot be changed silently
- **Merkle aggregation** — compressing many records into a single proof
- **Public witnessing** — anchoring data outside the control of its creator
- **SHA‑256** — collision‑resistant fingerprinting standard
- **Ed25519** — efficient, verifiable digital signatures
- **JSON Canonicalization (RFC 8785)** — deterministic data representation

ETIP acknowledges the foundational “Digital Notary” work by Haber and Stornetta (1991). It utilizes RFC 8785 for serialization, SHA‑256 for fingerprinting, Ed25519 for signatures, and aligns with the principles of Epistemic Integrity — ensuring justified belief in data states over time.

---

**4. Terminology**

| Term | Definition |
|------|------------|
| **Artifact** | The item being committed to the log — a file, message, state snapshot, or any digital object. |
| **Canonical bytes** | A deterministic byte representation of an artifact. JSON artifacts are canonicalized using JSON Canonicalization Scheme (RFC 8785). Non‑JSON artifacts are treated as raw bytes, with no transformation. |
| **Fingerprint** | The SHA‑256 digest of an artifact’s canonical bytes, serving as a unique content‑dependent identifier. |
| **Log** | An ordered, append‑only sequence of records, uniquely identified by a `log_id`. Records within a log are numbered with monotonically increasing `seq` values. |
| **Record** | A JSON object that is hashed according to the self‑hash rule to produce a `record_fp`. Three record types exist: `commit`, `batch_marker`, and `checkpoint`. |
| **Commit record** | A record (`type: "commit"`) that binds one artifact’s fingerprint into the log, linking backward to the previous record. |
| **Batch marker record** | A special record (`type: "batch_marker"`) that closes a batch of commit records. It contains the batch’s Merkle root, a link to the previous batch root, and the updated chain root. |
| **Link continuity** | Each record includes a `prev_record_fp` field that holds the `record_fp` of the immediately preceding record (`seq‑1`). This forms an unbreakable sequential chain. |
| **Checkpoint** | A record (`type: "checkpoint"`) that captures the current chain root at a specific sequence number and includes evidence of having been published to independent witnesses. |
| **Witness publication** | The act of submitting a chain root (and its operator signature) to an independent witness, which issues a timestamped, signed receipt. |
| **Independent witness channel** | A publication endpoint whose administrative control lies outside the log operator’s domain. Multiple, unrelated channels are required for ETIP‑Conformant operation. |
| **Batch** | A contiguous, non‑empty subsequence of commit records whose fingerprints are aggregated into a single Merkle root. |
| **Batch root** | The top‑most hash of a binary Merkle tree built from the fingerprints of all commit records in a batch (padding with zero‑hashes to a balanced tree when necessary). |
| **Chain root** | The running integrity accumulator for a log. It is computed as `SHA‑256(batch_root || previous_chain_root)` and updated whenever a new batch is closed. The current chain root is sealed in every batch marker and checkpoint. |
| **Self‑hash rule** | The method by which any record’s `record_fp` is computed: (1) remove the `record_fp` key from the record object; (2) serialize the remaining object with JCS; (3) compute SHA‑256 over those bytes; (4) insert the resulting hex string as the `record_fp` value. |
| **Merkle authentication path** | A list of sibling hashes (from leaf to root) that allows a verifier to recompute a batch root and thereby prove inclusion of a commit record in a particular batch. |

---

**5. What ETIP Guarantees & Threat Model**

ETIP provides the following **positive guarantees**, assuming correct implementation:

| Guarantee | Description |
|-----------|-------------|
| **Append‑only history** | Once appended, a record cannot be removed or re‑ordered without detection. |
| **Tamper‑evidence** | Any alteration of a past record changes its fingerprint, which alters the batch root, which in turn changes the chain root — breaking all subsequent chaining and invalidating witness receipts. |
| **Back‑dating resistance** | A record existed **no later than** the moment its containing batch’s chain root was witnessed externally. |
| **Deletion detection** | Deletion of a record changes the batch root; deletion of an entire batch breaks the `prev_batch_root` chain. Neither goes unnoticed. |
| **Sequential ordering** | Monotonically increasing sequence numbers and hash‑links provide a trusted temporal order (a lower‑bound on creation time relative to earlier witnessed records). |

**ETIP explicitly does not guarantee:**

| Non‑guarantee | Explanation |
|---------------|-------------|
| **Data correctness** | ETIP proves existence and sequence, not that the artifact’s content is factual. |
| **Completeness** | A log may omit records; ETIP does not prove that all relevant data was disclosed. |
| **Measurement accuracy** | The values inside artifacts may be erroneous or imprecise. |
| **Truth adjudication** | ETIP is silent on whether a claim is true, false, or misleading. |
| **Resistance to key compromise** | If the log operator’s Ed25519 signing key is stolen, an attacker can forge checkpoints. Mitigations include key rotation policies, multi‑witness co‑signing, and short‑lived keys. |
| **Long‑term cryptographic strength** | Future advances (e.g., quantum computing) may weaken SHA‑256 or Ed25519. Deployments should plan for crypto‑agility through algorithm identifiers in the log metadata. |

**Threat model refinements:**  
- A malicious operator may attempt to back‑date a record after the fact by rebuilding the chain. This is prevented if a witness published a prior chain root before the back‑dated entry, because the newly‑computed chain root would conflict with the witnessed one.  
- Collusion among witnesses could theoretically suppress evidence, but using multiple **independent witness classes** (see Section 10) mitigates this risk.  
- Key compromise is the most severe failure mode; it breaks all checkpoint authenticity. ETIP therefore recommends that operator keys be rotated frequently, with each rotation event itself witnessed.

---

**6. Core Mechanism & Canonicalization**

Every record in an ETIP log is a JSON object. Before any hashing takes place, the record must be reduced to a deterministic byte sequence — its **canonical bytes** — following these rules:

- **JSON artifacts**: Serialize the record **minus the `record_fp` key** using the JSON Canonicalization Scheme (RFC 8785). JCS sorts object keys, encodes strings as UTF‑8, and omits insignificant whitespace, ensuring the same logical object always produces the same bytes.
- **Non‑JSON artifacts**: Treat the artifact body as raw bytes without any transformation. The artifact’s `content_type` indicates how it should later be interpreted.
- **Canonical bytes** = a concatenation of:
  1. The JCS serialization of the record (without `record_fp`), for all record types.
  2. For commit records only, the artifact’s raw canonical bytes are hashed separately to form `artifact_fp`; they are **not** embedded in the record’s canonical bytes.

**Self‑hash computation** (`record_fp`):  
1. Temporarily remove the `record_fp` key from the record object.  
2. Apply JCS to the remaining object.  
3. Compute `SHA‑256` over the JCS output bytes.  
4. Set `record_fp` to the hexadecimal representation of that digest.  
This ensures each record’s fingerprint is a sealed hash of its own content *and* its backward link (`prev_record_fp`).

**Append‑only log & link continuity:**  
- Records MUST NOT be edited or deleted after being appended.  
- The `seq` field starts at 1 and increments by 1 for each new record, scoped to `log_id`.  
- Every record (except the very first) contains `prev_record_fp` set to the `record_fp` of the record with `seq‑1`. The first record uses `null`.  
This hash‑chain guarantees that any tampering with a single record cascades into all subsequent fingerprints, breaking the chain.

**Record creation & ordering:**  
The log operator accumulates `commit` records. Periodically (e.g., after a fixed count or time interval), a `batch_marker` record is appended to close the batch. Checkpoint records may be appended at any time to capture the current chain root and publish it to witnesses.

---

**7. Merkle Tree Compression**

Merkle tree compression reduces an ordered set of commit records into a single verifiable digest — the batch root — enabling compact inclusion proofs without storing the entire log. This section formalizes the construction.

---

**7.1 Batch construction**

The log operator groups **consecutive commit records** (type `"commit"`) into a batch. The batch boundary is a local policy parameter, for example:

- A fixed number of commit records (e.g., every 1000 commits), or
- A fixed time window (e.g., every hour).

Every batch **MUST contain at least one commit record**; an empty batch is not permitted and, if no commits occurred during the window, no batch marker is produced.

For each batch, a binary Merkle tree is built:

**Leaves:**  
Compute the `record_fp` of each commit record (as defined by the self‑hash rule). The tree’s leaves are the SHA‑256 digests of these `record_fp` values: `leaf_i = SHA‑256(record_fp_i)`.

**Internal nodes:**  
For any pair of child node bytes `left` and `right` (each 32 bytes), the parent node is `SHA‑256( left || right )`.

**Tree structure:**  
The tree MUST be **balanced to the left**. All leaves are placed at the same depth. If the number of leaves is not a power of two, the shortest tree is built by adding **padding leaves** with the value `SHA‑256("")` (the hash of empty bytes, i.e., `0xe3b0c44298fc1c14…`) until the leaf count reaches the next power of two. This construction is the traditional balanced binary Merkle tree; alternative configurations (like an unbalanced tree with explicit leaf count) may be defined in extensions but are not part of this specification.

The **batch root** is the top‑most hash of this tree.

**Batch marker record:**  
After the last commit record of a batch, the operator appends a special record of type `"batch_marker"` that seals the batch and links it to the chain root. The batch marker is a regular log record, subject to the same self‑hash rule and link continuity (`prev_record_fp` points to the preceding record, which will be the last commit record).

The batch marker carries the following fields in addition to the common record fields:

```json
{
  "spec": "ETIP-2.1",
  "log_id": "string",
  "seq": 789,
  "type": "batch_marker",
  "prev_record_fp": "hex",
  "batch_root": "hex",
  "prev_batch_root": "hex",
  "chain_root": "hex",
  "record_fp": "hex"
}
```

- `batch_root` — the Merkle root of the batch being closed.  
- `prev_batch_root` — the `batch_root` of the immediately preceding batch marker, or `null` for the first batch.  
- `chain_root` — the running chain root **after** incorporating this batch. It is computed as `SHA‑256( batch_root || previous_chain_root )`, where `previous_chain_root` is the `chain_root` of the preceding batch marker (or `null` for the first batch, yielding `SHA‑256( batch_root )`).

The chain root is therefore a cumulative hash that anchors all batches up to this point.

---

**7.2 Proof of inclusion**

To prove that a specific commit record is contained in a given batch, a prover supplies:

1. The commit record itself (including its `record_fp`).  
2. A **Merkle authentication path** — the ordered list of 32‑byte sibling hashes from the leaf up to the batch root.  
3. The `batch_root` as published in the batch marker record.  

Verification proceeds as follows:

- Compute `leaf = SHA‑256(record_fp)`.  
- For each sibling in the path (from leaf to root), compute the parent hash accordingly: if the leaf was the left child of a pair, compute `parent = SHA‑256( leaf || sibling )`; if it was the right child, `parent = SHA‑256( sibling || leaf )`. The path must indicate left/right ordering (commonly by appending a single flag per step).  
- The result after consuming all siblings must match the given `batch_root`.  

If the recomputed root matches, the commit record is proven to be part of that batch with the same integrity guarantee as if the entire log were present.

---

**7.3 Chaining of batch roots (chain root)**

Batch markers themselves form a linear chain through their `prev_batch_root` fields. This provides longitudinal integrity: any tampering with a batch root would change the `batch_root` of that batch’s marker, which would change the chain root stored in that same marker and all subsequent chain roots. The **chain root** is therefore the definitive integrity snapshot of the entire log.

When the operator later creates a checkpoint record (Section 12), it records the current `chain_root` and publishes it to witnesses. Witness receipts bind that chain root to a point in time, thus sealing the entire log history up to that batch.

Because the chain root depends on all prior batch roots, it protects against deletion or reordering of entire batches. Any attempt to remove a batch marker (and its batch) would break the `prev_batch_root` link in the next marker, and the chain root computation would no longer reproduce the witnessed value.

---

**8. Levels of Use / Conformance**

ETIP defines two conformance levels. They are cumulative: the higher level requires all the capabilities of the lower one.

---

**8.1 ETIP‑Compatible (Basic Integrity)**

A log implementation is **ETIP‑Compatible** if it satisfies all of the following:

| Requirement | Description |
|-------------|-------------|
| **Canonicalization** | All records are serialized according to RFC 8785 (or treated as raw bytes for non‑JSON artifacts) before hashing. |
| **Fingerprinting** | SHA‑256 is used for all digest computations. |
| **Append‑only sequencing** | Records are assigned strictly increasing `seq` numbers scoped to a `log_id`. |
| **Link continuity** | Each record includes `prev_record_fp` referencing the previous record’s fingerprint (or `null` for the first). |
| **Self‑hash rule** | All record fingerprints are computed as specified (removing `record_fp`, canonicalizing, hashing). |
| **Batch markers** | Commit records are grouped into batches; each batch is closed by a valid batch marker record containing the batch root, previous batch root, and the updated chain root. |
| **Immutability** | Once appended, records are never edited or deleted. |

An ETIP‑Compatible log provides internal consistency, tamper‑evidence within its own sequence, and detection of any modification, but makes **no time claims** and does **not involve external witnessing**.

---

**8.2 ETIP‑Conformant (Witnessed Integrity)**

A log implementation is **ETIP‑Conformant** if it satisfies all requirements for ETIP‑Compatible **and additionally**:

| Requirement | Description |
|-------------|-------------|
| **Checkpoint generation** | The log periodically (at least once per 24‑hour window) creates a checkpoint record that captures the current `chain_root` and signs it with the log’s Ed25519 private key. |
| **Witness publication** | The operator publishes the signed checkpoint (chain root and signature) to **at least two independent witness channels** from distinct witness classes (see Section 10). |
| **Witness receipt preservation** | The operator stores witness receipts and references them in the checkpoint’s `publications` field, making them retrievable for auditors. |
| **Public key availability** | The log’s Ed25519 public key is published and independently verifiable (e.g., via a trusted static configuration or a separate attested channel). |

An ETIP‑Conformant log guarantees **“existed no later than”** for any record whose batch is covered by a witnessed checkpoint. The witness receipts provide external, non‑repudiable evidence of the log’s state at a point in time. Auditors can independently verify the full log history by recomputing the chain root and checking witness signatures.

---

**9. Time Semantics**

ETIP does not claim to prove an exact creation timestamp for any record. Instead, it provides two bounded temporal guarantees derived from sequential chaining and external witnessing.

---

**9.1 Upper bound: “existed no later than”**

A record’s existence can be bounded above by the earliest witness receipt that covers its batch.

- Suppose commit record *R* belongs to batch *B*. Batch *B* is closed with a batch marker that updates the chain root to *C*.  
- Chain root *C* is subsequently included in a checkpoint and published to an independent witness. The witness issues a receipt with a timestamp *T*.  
- Then *R* **must have existed no later than time** *T*.  

In other words, the data inside *R* cannot have been created **after** the moment the witness saw the chain root that incorporates that data. This is a forensic upper bound — it caps the latest possible creation time.

When multiple witnesses publish receipts at different times, the **earliest** receipt timestamp provides the tightest upper bound for all records covered by that checkpoint.

---

**9.2 Lower bound: “could not have existed before” (informative)**

Because records in a log are strictly ordered by `seq` and each record’s fingerprint includes the fingerprint of its predecessor, the log also provides an **implied lower bound** on creation time.

- Suppose record *R* at `seq = n` has a predecessor record *S* at `seq = n‑1`.  
- *S* belongs to an earlier batch, and its batch’s chain root was witnessed at time *T_earlier*.  
- Since *R* links backward to *S*, *R* **cannot have been created before** *T_earlier* — unless the operator had the ability to predict *S*’s fingerprint before *S* was appended and witnessed, which is infeasible under SHA‑256 pre‑image resistance.

Thus, for two records *A* (earlier) and *B* (later), the time *A* was witnessed serves as a lower bound for *B*, and the time *B* was witnessed serves as an upper bound for *A*. Together, witnesses at regular intervals produce a tightening time window on the entire log.

**Important caveat:** The lower‑bound guarantee depends on trusting the log operator’s internal ordering. A malicious operator could, in principle, delay publishing a checkpoint and later insert records with earlier sequence numbers if no prior witness publication had constrained them. For this reason, frequent checkpointing (every 24 hours or less) is strongly recommended.

---

**9.3 Epistemic decay (informative)**

While the cryptographic integrity of a record does not degrade over time — SHA‑256 and Ed25519 signatures remain valid indefinitely against today’s adversaries — the **epistemic value** of old records may diminish in audit contexts.

- **Epistemic value** is the degree to which a record supports a justified belief about a fact at a past point in time.  
- Over long periods, changes in context, regulatory frameworks, or the discovery of omission may reduce the relevance of an old record, even though its mathematical integrity is intact.  

Implementers **SHOULD** define a **decay horizon** at the verification layer. A recommended default is **400 days** from the date of the first witness receipt covering a record. After this horizon, the record should be flagged as **stale** during audit verification, prompting reviewers to consider its continued relevance. This is a policy decision, not a protocol enforcement, and may vary by use case (e.g., financial audits may require shorter horizons; academic archives may tolerate longer ones).

Witness class may influence decay: records witnessed by a **Class C** timestamp authority (RFC 3161) may carry a longer decay horizon than those witnessed only by a **Class B** participant, because the legal standing of the timestamp may extend the record’s practical useful life.

---

**10. Witnessing & Discovery**

A record’s integrity claim remains self‑contained as long as it stays inside the log operator’s system. It becomes **forensically meaningful** only when an external, independent party attests to having seen it at a point in time.

---

**10.1 Witnessing requirement**

To achieve ETIP‑Conformant status, a log operator MUST:

- Publish the current chain root to **at least two (2) independent witness channels** within every **24‑hour period**.  
- Retain and reference each witness’s receipt in the log’s checkpoint records.  

If no checkpoint has been witnessed within 24 hours of the prior checkpoint, the log’s temporal guarantees are considered **lapsed** for records appended since the last witnessed checkpoint.

---

**10.2 Witness classes**

Witnesses are categorized into three classes based on their role, independence, and the epistemic weight they contribute.

| Class | Description | Examples |
|-------|-------------|----------|
| **Class A — Public Service** | Independent, publicly accessible observers with no direct stake in the data. They exist to provide transparency and are run by unrelated third parties. | Public archive services, data integrity nonprofits, research institutions offering free witnessing endpoints. |
| **Class B — Conforming Participants** | Organizations that have a direct or indirect relationship with the log operator (industry peers, consortium members, auditors) but operate under independent administration. | Trade associations, ESG consortia, partner NGOs, institutional auditors. |
| **Class C — Timestamp Authorities** | Certified timestamping services that provide legally recognized temporal attestation, typically under standards such as RFC 3161 or eIDAS. | Government‑accredited timestamp authorities, qualified trust service providers. |

Witnesses from **Class C** carry the highest individual epistemic weight due to legal and regulatory standing. Class A witnesses provide broad public visibility. Class B witnesses contribute distributed trust within a specific ecosystem.

Blockchain‑based witnessing services are **not recommended** unless they provide demonstrable unique value without introducing consensus overhead, energy waste, or economic token dependencies that conflict with ETIP’s infrastructure philosophy (Section 11).

---

**10.3 Recommended witness class combinations**

To ensure high epistemic weight and resilience against single‑witness failure or collusion, the following combinations are recommended:

| Combination | Use Case | Rationale |
|-------------|----------|-----------|
| **Class A + B** | Standard corporate accountability (e.g., ESG reporting) | Bridges public transparency (A) with industry peer‑review (B). |
| **Class B + B** | Consortium‑based audit trails | Distributes trust among multiple industry participants without requiring public disclosure. |
| **Class A + C** | High legal assurance | Combines public visibility (A) with certified temporal authority (C); appropriate for regulated disclosures. |
| **Class A + B + C** | Maximum forensic integrity | All three classes combined provide the strongest possible non‑repudiation for mission‑critical audit timelines. |

Using two witnesses of the **same** class is acceptable but provides lower epistemic diversity. The operator should select the combination that best matches their audit and regulatory context.

---

**10.4 Witness communication interface**

Every witness MUST expose a minimal HTTP API. The use of HTTPS (TLS 1.2 or higher) is **REQUIRED** for all witness communication to protect the integrity and confidentiality of submissions.

**POST /witness**

Submits a chain root for witnessing. The request body is a JSON object:

```json
{
  "chain_root": "<hex-encoded SHA-256 chain root>",
  "signature": "<Ed25519 signature over the chain_root, hex-encoded>"
}
```

- `chain_root` — the current chain root that the log operator wishes to have witnessed.  
- `signature` — the Ed25519 signature over the raw 32 bytes of the `chain_root`, produced by the log operator using the log’s private key. The witness **MUST** verify this signature against the log’s public key (obtained via the configuration methods in Section 10.5) before issuing a receipt. This prevents unauthorized parties from submitting fraudulent chain roots on behalf of the log.

On success, the witness returns HTTP **202 Accepted** with a receipt body:

```json
{
  "chain_root": "<hex>",
  "receipt_id": "<opaque string>",
  "witness_id": "<witness url or identifier>",
  "witness_timestamp": "<ISO 8601>",
  "witness_signature": "<Ed25519 signature, hex-encoded>"
}
```

- `receipt_id` — a unique identifier for this receipt, used for later retrieval.  
- `witness_signature` — the witness’s Ed25519 signature over the canonical bytes of the receipt object **minus the `witness_signature` field**, computed using the same self‑hash pattern as log records. Canonicalization uses JCS (RFC 8785). This ensures the receipt itself is tamper‑evident.

If the signature verification fails, the witness returns HTTP **403 Forbidden** with an error message.

**GET /witness/{chain_root}**

Retrieves a previously issued receipt for the given chain root.

- Returns HTTP **200 OK** with the receipt JSON if a receipt exists.  
- Returns HTTP **404 Not Found** if no receipt exists for the given chain root.

---

**10.5 Witness configuration methods**

There is no centralized global registry of witnesses. Implementors discover and configure witnesses using one or more of the following methods:

| Method | Status | Description |
|--------|--------|-------------|
| **Static configuration** | **RECOMMENDED** | A local configuration file lists witness URLs, their public keys, and their classes. This is the simplest and most reliable method for production use. |
| **DNS TXT records** | **OPTIONAL** | Witness details may be published as a TXT record under a domain (e.g., `_etip-witness.example.com`). This allows domain‑controlled discovery but introduces DNS as a dependency. |
| **Peer‑to‑peer manifest** | **EXPERIMENTAL** | A signed manifest of available witnesses can be exchanged between peer operators. This method is under evaluation and may change in future versions. |

Static configuration is RECOMMENDED because it avoids external runtime dependencies and simplifies auditing. Regardless of discovery method, all witness public keys MUST be obtained through an independent, trusted channel before the first submission.

---

**11. Infrastructure Philosophy**

ETIP is designed to run in constrained environments. It does not require large data centers, continuous high‑performance compute, or specialized hardware. It can operate on edge workers, small devices, and low‑power systems.

---

**11.1 Guiding principle**

> *Integrity should not depend on heavy infrastructure.*

Many data integrity systems have inherited the operational cost of blockchains — global consensus, proof‑of‑work, economically incentivized validators, continuous network participation. ETIP rejects these assumptions. Chaining, fingerprinting, and witnessing are computationally lightweight by design. SHA‑256 and Ed25519 are efficient on modern hardware, and even on constrained platforms (embedded ARM, low‑power SoCs, small‑footprint virtual machines), the cost of hashing and signing is negligible.

A system meant to improve accountability should not increase environmental cost. The Merkle tree compression ensures that even the smallest system‑on‑chip can participate without burden to water, air, nature, or the surrounding ecosystem. There is no mining, no staking, no continuous network chatter.

---

**11.2 Operational characteristics**

| Property | ETIP | Typical blockchain |
|----------|------|-------------------|
| **Energy per record** | Microjoules (one SHA‑256 + one Ed25519 sign) | Kilowatt‑hours to megawatt‑hours (consensus) |
| **Hardware required** | Any CPU with ~1 MB RAM | Data center, specialized ASICs, or high‑stake validators |
| **Network dependency** | Occasional HTTPS POST to witness | Continuous peer‑to‑peer connectivity |
| **Storage growth** | ~200 bytes per record + periodic batch marker | Full ledger replication |
| **Latency** | Milliseconds (local append) | Seconds to minutes (block time) |

---

**11.3 Deployment scenarios**

ETIP is suitable for:

- **Edge logging** — IoT devices, remote sensors, or offline systems that record events in the field and later synchronize with witnesses.  
- **Embedded audit trails** — Firmware update logs, configuration change trackers, and build‑process artifact chains.  
- **Low‑resource environments** — Regions or organizations with limited internet connectivity, intermittent power, or minimal compute capacity.  
- **ESG and sustainability reporting** — Where the energy footprint of the audit system itself is subject to scrutiny; ETIP avoids undermining the environmental goals it helps report on.  

The operator can run ETIP on a Raspberry Pi, a cloud function, or a dedicated server — the protocol imposes no minimum hardware requirement beyond the ability to compute SHA‑256 and Ed25519.

---

**11.4 Environmental and social responsibility**

ETIP’s design intentionally biases toward **low‑impact operation**. There is no token, no incentive to compete for block space, and no reason to increase computational expenditure beyond what is necessary for integrity. The protocol’s carbon footprint is effectively zero per record.

This aligns ETIP with the broader goals of sustainable technology: tools that solve problems without creating new ones. If an audit system consumes more resources than the activity it audits, it has failed its own purpose. ETIP ensures that the integrity layer is never the environmental bottleneck.

---

**12. Data Model (Schemas)**

All fingerprints are SHA‑256 over canonicalized data. Signatures use Ed25519. The following schemas define the complete record types in an ETIP log.

---

**12.1 Commit record**

A commit record binds one artifact into the log. It is the most common record type.

```json
{
  "spec": "ETIP-2.1",
  "log_id": "string",
  "seq": 123,
  "type": "commit",
  "prev_record_fp": "hex-or-null",
  "record_fp_alg": "sha-256",
  "artifact_fp": "hex",
  "meta": {
    "content_type": "string",
    "external_ref": "string"
  },
  "record_fp": "hex"
}
```

| Field | Required | Description |
|-------|----------|-------------|
| `spec` | Yes | Protocol version identifier, always `"ETIP-2.1"`. |
| `log_id` | Yes | Unique identifier for the log, scoping all records. |
| `seq` | Yes | Monotonically increasing sequence number within this log. |
| `type` | Yes | Always `"commit"` for commit records. |
| `prev_record_fp` | Yes | The `record_fp` of the preceding record, or `null` for the first record. |
| `record_fp_alg` | Yes | Hash algorithm used; always `"sha-256"`. |
| `artifact_fp` | Yes | SHA‑256 fingerprint of the artifact’s canonical bytes. |
| `meta.content_type` | Yes | MIME type or other type indicator for the artifact. |
| `meta.external_ref` | No | Optional pointer to the artifact’s storage location. |
| `record_fp` | Yes | Self‑hash per the self‑hash rule (Section 6). |

**Self‑hash rule for commit records:** Remove `record_fp`, apply JCS to the remaining object, compute SHA‑256, and set the result as `record_fp`.

---

**12.2 Batch marker record**

A batch marker closes a batch of commit records and anchors the batch root into the chain root.

```json
{
  "spec": "ETIP-2.1",
  "log_id": "string",
  "seq": 789,
  "type": "batch_marker",
  "prev_record_fp": "hex",
  "record_fp_alg": "sha-256",
  "batch_root": "hex",
  "prev_batch_root": "hex-or-null",
  "chain_root": "hex",
  "record_fp": "hex"
}
```

| Field | Required | Description |
|-------|----------|-------------|
| `spec` | Yes | Protocol version, `"ETIP-2.1"`. |
| `log_id` | Yes | Same as the log’s `log_id`. |
| `seq` | Yes | Sequence number, immediately after the last commit record in the batch. |
| `type` | Yes | Always `"batch_marker"`. |
| `prev_record_fp` | Yes | `record_fp` of the last commit record in the batch. |
| `record_fp_alg` | Yes | `"sha-256"`. |
| `batch_root` | Yes | Merkle root of the batch being closed (Section 7). |
| `prev_batch_root` | Yes | The `batch_root` of the previous batch marker, or `null` for the first batch. |
| `chain_root` | Yes | `SHA‑256(batch_root \|\| previous_chain_root)`. For the first batch, `previous_chain_root` is treated as absent (hash only `batch_root`). |
| `record_fp` | Yes | Self‑hash per the self‑hash rule. |

The chain root stored in the batch marker is the **post‑update** value — it includes this batch’s contribution. It becomes the `previous_chain_root` for the next batch marker.

---

### 12.3 Checkpoint Record

A checkpoint record captures the state of the log at a moment in time and attests that the chain root has been published to external witnesses. It is the evidence that moves the log from ETIP‑Compatible to ETIP‑Conformant.

```json
{
  "spec": "ETIP-2.1",
  "log_id": "string",
  "seq": 1001,
  "type": "checkpoint",
  "prev_record_fp": "hex",
  "covers_seq": 1000,
  "chain_root": "hex",
  "operator_signature": "hex",
  "witness_receipts": [
    {
      "witness_id": "url",
      "chain_root": "hex",
      "receipt_id": "string",
      "witness_timestamp": "ISO8601",
      "witness_signature": "hex"
    }
  ],
  "checkpoint_fp": "hex"
}
```

| Field | Required | Description |
|-------|----------|-------------|
| `spec` | Yes | `"ETIP-2.1"`. |
| `log_id` | Yes | Same log identifier. |
| `seq` | Yes | Sequence number of this checkpoint record. |
| `type` | Yes | `"checkpoint"`. |
| `prev_record_fp` | Yes | `record_fp` of the immediately preceding record (typically a `batch_marker` or `commit` record). |
| `covers_seq` | Yes | The `seq` of the last commit record (or `batch_marker`) whose chain root is being sealed. It establishes the upper bound of the log that is witnessed. |
| `chain_root` | Yes | The chain root at the moment the checkpoint is taken (the value stored in the most recent `batch_marker`). |
| `operator_signature` | Yes | Ed25519 signature over the raw 32‑byte `chain_root`, produced by the log operator’s private key. This signature is verified by witnesses before they issue a receipt. |
| `witness_receipts` | Yes | Array of at least two witness receipts, each containing the witness’s timestamp and signature. The receipts themselves are tamper‑evident objects (signed by the witness). |
| `checkpoint_fp` | Yes | Self‑hash of the checkpoint record (excluding this field). |

**Self‑hash rule for checkpoint records:** identical to other record types — remove `checkpoint_fp`, apply JCS, hash with SHA‑256.

**Inclusion of witness receipts:** The receipts in the `witness_receipts` array are identical to the objects returned by the witnesses’ `POST /witness` endpoint. By embedding them directly in the log record, the checkpoint becomes self‑contained: an auditor can verify the operator’s signature, then verify each witness signature, and thereby confirm the chain root was observed externally at the recorded timestamps — all without retrieving separate witness archives.

---

### 13. ESG Context

ETIP does not stop companies from changing numbers. It makes those changes **visible**.

In sustainability reporting (ESG), data evolves: baselines are recalculated, methodologies change, errors are discovered and corrected. What matters for trust is not a single snapshot, but the **timeline** — the sequence of what was claimed, when, and how it was later revised.

ETIP provides the infrastructure for that timeline:

- An auditor can replay the log and see exactly when a number appeared, when it was superseded, and whether any record was retroactively altered.  
- Witness receipts prove that a particular claim existed at a particular time, before any later scandal or restatement could have motivated its fabrication.  
- Batch and chain roots allow auditors to verify an entire reporting history from a single chain root, without trusting the company’s internal archiving.

Critically, ETIP itself does **not** interpret ESG data; it only preserves the historical record. The protocol is compatible with any reporting framework (GRI, SASB, ESRS, ISSB) and does not enforce any particular disclosure standard.

---

### 14. Mandatory Non‑Claims

All implementations of ETIP MUST prominently state that the protocol:

- **Does not verify correctness** of the data.  
- **Does not certify compliance** with any regulation or standard.  
- **Does not adjudicate truth** or falsity of statements.  
- **Does not ensure completeness** of records.  
- **Does not issue assets** or tokens of any kind.  
- **Does not protect against key compromise** — a stolen operator key may allow forged checkpoints.  
- **Does not offer perpetual cryptographic security** — future advances may weaken SHA‑256 or Ed25519.  

ETIP **only** preserves temporal integrity: the order and existence of records, and the inability to alter them without breaking evidence.

These non‑claims must appear in documentation, on‑premise interfaces, and any public‑facing tools that consume ETIP logs. Auditors and users must understand exactly what ETIP can and cannot deliver.

---

### Changelog

**v1.2 (Revised)**  
- Clarified self‑hash computation with explicit key‑removal rule.  
- Added formal `batch_marker` record schema and chain root definition.  
- Removed misleading “superseded” language from deletion guarantee.  
- Specified that the log operator signs the chain root before witness submission, and that witnesses verify this signature.  
- Added explicit canonicalization rule for witness receipt signing (self‑hash pattern).  
- Renamed conformance levels to **ETIP‑Compatible** and **ETIP‑Conformant** for clarity.  
- Added lower‑bound time guarantee (informative).  
- Expanded epistemic decay guidance with witness‑class influence.  
- Updated checkpoint record schema to include `operator_signature` and inline `witness_receipts`.  
- Added key compromise and crypto‑agility to threat model and non‑claims.  
- Clarified that every batch must contain at least one commit record.  

---


© 2026 Y. Chen. Licensed under the Apache License, Version 2.0.  
Epistemic Temporal Integrity Protocol | Zero‑Dependency HTML5
