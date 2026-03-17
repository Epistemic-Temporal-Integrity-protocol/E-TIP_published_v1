# E-TIP_published_v1
 RFC: ETIP-v1 - Epistemic Temporal Integrity Protocol body { font-family: "Courier New", Courier, monospace; line-height: 1.4; color: #000; max-width: 850px; margin: 20px auto; padding: 20px; background: #fff; } .rfc-container { border: 1px solid #000; padding: 40px; } header { border-bottom: 2px solid #000; margin-bottom: 20px; padding-bottom: 10px; display: flex; justify-content: space-between; font-weight: bold; } h1 { text-align: center; font-size: 1.2em; text-transform: uppercase; text-decoration: underline; } h2 { font-size: 1.1em; text-decoration: underline; margin-top: 25px; border-bottom: 1px solid #eee; padding-bottom: 5px; } .disclaimer-box { border: 2px solid #000; padding: 15px; background: #f0f0f0; font-weight: bold; margin-bottom: 20px; font-size: 0.9em; } nav { border: 1px solid #000; padding: 15px; margin-bottom: 30px; background: #f9f9f9; } nav ul { list-style: none; padding-left: 0; } pre { background: #eee; padding: 10px; border: 1px dotted #000; overflow-x: auto; white-space: pre-wrap; } .diagram { font-family: "Courier New", monospace; white-space: pre; background: #fff; padding: 15px; border: 1px solid #000; margin: 20px 0; line-height: 1.2; font-size: 0.9em; } footer { margin-top: 40px; text-align: center; font-size: 0.9em; border-top: 1px solid #000; padding-top: 10px; }

RFC: ETIP-v1

epistemic.center

Category: Standards Track

March 2026

Epistemic Temporal Integrity Protocol (ETIP) v1
=================================================

LEGAL NOTICE: THIS PROTOCOL IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND. IN NO EVENT SHALL THE AUTHORS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE PROTOCOL OR THE USE OR OTHER DEALINGS IN THE PROTOCOL.

**Table of Contents**

*   0\. [License & Legal Shield](#license)
*   1\. [Abstract](#abstract)
*   2\. [System Topology (Diagram)](#topology)
*   3\. [Integrity Mechanism (Diagram)](#chaining)
*   4\. [Conformance Levels](#conformance)
*   5\. [Infrastructure Requirements](#infra)
*   6\. [The Witnessing Rule (A, B, C Classes)](#witness)
*   7\. [Data Model & Witness Schema](#schemas)
*   8\. [Verification Algorithm](#verification)
*   9\. [Appendix A: Reference Implementation](#appendix)

0\. License & Legal Shield
--------------------------

This protocol is licensed under the **Apache License, Version 2.0**. Implementers are solely responsible for deployment security. No guarantee is provided regarding the future resilience of SHA-256 or Ed25519 against algorithmic advances.

1\. Abstract
------------

ETIP-v1 defines a low-energy protocol for decentralized temporal data integrity. It utilizes **Recursive SHA-256 Chaining** and **Identity-Linked Signatures** to prevent history manipulation. The protocol is designed to operate within a **10ms CPU cycle**, ensuring that auditing capabilities function as a "rounding error" in global infrastructure costs.

2\. System Topology
-------------------

\[Operator Internal Log\] ----> \[Daily Root Hash\] ----> \[Independent Witness\] (Continuous Recording) (h\_roll\_root) (A, B, and/or C) | | | | 1. Append Records | 2. Submit to Witness | 3. Issue Receipt | 4. Start New Day <---------------------------------+

The topology ensures that internal, high-frequency activity is periodically "anchored" to external, independent evidence channels, converting subjective logs into objective forensic facts.

3\. Integrity Mechanism
-----------------------

+---------------------+ | Previous h\_roll |-----\\ +---------------------+ \\ +---------------------+ +---->| SHA-256 Digest |----> \[Next h\_roll\] +---------------------+ / +---------------------+ | Current record\_fp |-----/ +---------------------+

Each record incorporates the cumulative state of all prior records. A single-byte change in history causes a total divergence in all subsequent **h\_roll** values, making "Shadow Logs" mathematically detectable.

4\. Conformance Levels
----------------------

*   **ETIP-Compatible:** Implementation of sequential hashing and basic `h_prev` linking.
*   **ETIP-Conformant:** Implementation of **Ed25519 signatures**, **Recursive h\_roll**, and **Multi-Class Witnessing** (at least two independent channels).

5\. Infrastructure Requirements
-------------------------------

*   **CPU Limit:** Single-record processing MUST NOT exceed **10ms**.
*   **Blob-First:** Artifacts are processed as raw byte-streams to minimize serialization overhead.

6\. The Witnessing Rule (Classes A, B, C)
-----------------------------------------

Consistency is guaranteed through redundant external publication to three witness classes:

*   **Witness A (Public Utility):** Neutral, high-availability service (e.g., a public ledger or transparency log).
*   **Witness B (Peer Witness):** Mutual cross-witnessing between independent operators.
*   **Witness C (TSA):** RFC 3161 Time Stamping Authority providing legally recognized time-certs.

7\. Data Model & Witness Schema
-------------------------------

### 7.1 Commit Record

{
  "v": "1.0",
  "log\_id": "string",
  "seq": 123,
  "h\_art": "hex",           // SHA-256 of artifact
  "h\_roll": "hex",          // SHA256(h\_roll\_prev + record\_fp)
  "pub\_key": "hex",         // Operator Identity
  "sig": "hex",             // Ed25519(v + log\_id + seq + h\_roll)
  "record\_fp": "hex"        // Self-hash
}
    

8\. Verification Algorithm
--------------------------

1.  **Recompute:** Regenerate the hash chain from the last known-good state.
2.  **Verify:** Confirm Ed25519 signatures for all records.
3.  **Divergence Check:** Compare the final **h\_roll** against the Witness Receipts from Classes A, B, or C. Any divergence indicates a **Temporal Fork**.

9\. Appendix A: Reference Implementation
----------------------------------------

Implementers SHOULD utilize hardware-accelerated SHA-256 instructions. A conforming implementation must be able to verify 1,000 records in under 1 second of total CPU time.

\[End of RFC-ETIP-1.5\]
