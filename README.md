<h1>Epistemic Temporal Integrity Protocol (ETIP) v1.0</h1>
<p><strong>Category:</strong> Standards Track<br>
<strong>Maintainer:</strong> epistemic.center<br>
<strong>Status:</strong> RFC 1.0 (March 2026)</p>

<hr>

<h2>1. Positioning & Abstract</h2>
<p>The <strong>Epistemic Temporal Integrity Protocol (ETIP)</strong> specifies a standardized, platform-agnostic, tamper-evident record and verification protocol. It provides a mathematical guarantee that a digital record existed at a specific point in time and has remained unchanged since its creation. By anchoring recursive mathematical chains (SHA-256) to a <strong>PGP-verified identity (Ed25519)</strong> and independent external witnesses, ETIP ensures that data authenticity is immutable and globally verifiable.</p>
<ul>
  <li><strong>Non-Asset System:</strong> ETIP does not describe tokens or "minting" and is not an asset system.</li>
  <li><strong>Integrity vs. Truth:</strong> The protocol certifies <em>persistence</em> and <em>sequence</em>; it does not verify the "truthfulness" of the content.</li>
  <li><strong>Platform Agnostic:</strong> Designed to function as a nominal cost in any data processing pipeline, regardless of architecture or provider.</li>
</ul>

<h2>2. Historical Context & Priors</h2>
<p>ETIP is a modern synthesis of four decades of cryptographic research, building upon the following specific historical precedents:</p>
<ul>
  <li><strong>Haber & Stornetta (1991):</strong> For the invention of <strong>Hash Chaining</strong>. This solved the problem of digital document tampering by making each timestamp dependent on the one before it, removing the need for a trusted central party.</li>
  <li><strong>Bayer, Haber & Stornetta (1993):</strong> For the introduction of Merkle-style aggregation, allowing thousands of documents to be represented by a single <strong>Root Hash</strong>. This provides the foundation for ETIP’s <strong>Hourly Bundling</strong>.</li>
  <li><strong>The New York Times Public Witness (1995):</strong> Codifying the practice of anchoring internal data to an <strong>Independent Witness Channel</strong> (e.g., Surety’s weekly publication in the NYT classifieds) to prevent secret history rewriting.</li>
  <li><strong>Daniel J. Bernstein (2011):</strong> For <strong>Ed25519</strong>, the high-performance Edwards-curve digital signature algorithm. Its efficiency allows ETIP to remain performant in restricted environments (e.g., &lt;10ms CPU windows).</li>
  <li><strong>NIST/NSA:</strong> For <strong>SHA-256</strong> (Secure Hash Algorithm 2), providing the collision-resistant standard used for the protocol's primary fingerprinting.</li>
  <li><strong>RFC 8785 (2020):</strong> For the <strong>JSON Canonicalization Scheme (JCS)</strong>, ensuring that a record hashed on any device produces the exact same Fingerprint.</li>
</ul>

<hr>

<h2>3. Conformance Levels</h2>

<h3>3.1 ETIP-Compatible (Internal Integrity)</h3>
<p><strong>Requirements:</strong> Canonicalize artifacts via JCS, fingerprint with <strong>SHA-256</strong>, append sequentially, and maintain <strong>Link Continuity</strong>. 
<br><em>Internal Logic:</em> Artifacts captured within a 60-minute window are consolidated into an <strong>Hourly Bundle</strong>. No time claims are permitted at this level; it serves strictly as an internal proof of sequence.</p>

<h3>3.2 ETIP-Conformant (Forensically Sealed)</h3>
<p><strong>Requirements:</strong> Satisfy 3.1, produce <strong>Checkpoints</strong> signed with an <strong>Ed25519 PGP key</strong>, and publish the terminal Root Hash to at least <strong>two (2) Independent Witness Channels</strong> every 24 hours.
<br><em>Time Semantics:</em> Permits "No later than" claims for the ranges covered by the publication evidence.</p>

<hr>

<h2>4. Time Semantics & Epistemic Decay</h2>
<p>ETIP supports <strong>upper-bound time ("no later than")</strong> only for ranges covered by ETIP-Conformant checkpoints. ETIP does not prove exact creation time because an author can falsify their own clock, but they cannot falsify the history of an independent witness.</p>

<h3>4.1 Epistemic Decay (Informative)</h3>
<p>While cryptographic integrity does not expire, the "Epistemic Value" (relevance/trustworthiness) of a record may decay. Implementers SHOULD define <strong>Decay Horizons</strong> at the verification layer (e.g., records are considered stale after 400 days if not re-witnessed or re-anchored).</p>

<hr>

<h2>5. Witness Classes (The Rule of Two)</h2>
<p>A log is only <strong>Forensically Sealed</strong> if the Root Hash is published to a combination of:</p>
<ul>
  <li><strong>Class A (Public Utility):</strong> Immutable public ledgers or decentralized networks.</li>
  <li><strong>Class B (Independent Peer):</strong> A separate server/partner environment not under the log operator's control.</li>
  <li><strong>Class C (Certified Timestamp):</strong> An RFC 3161 Time Stamping Authority (TSA).</li>
</ul>

<hr>

<h2>6. Data Model & Schema</h2>

<h3>6.1 Commit Record</h3>
<pre bgcolor="#f6f8fa" style="padding:15px;">
{
  "spec": "ETIP-1.0",
  "log_id": "string",
  "seq": 123,
  "type": "commit",
  "prev_record_fp": "hex-or-null",
  "artifact_fp": "hex",
  "pgp_fpr": "hex",
  "record_fp": "hex"
}
</pre>
<p><em>Self-hash rule: <code>record_fp = SHA256( JCS( record_minus_record_fp ) )</code></em></p>

<h3>6.2 Checkpoint Record</h3>
<pre bgcolor="#f6f8fa" style="padding:15px;">
{
  "spec": "ETIP-1.0",
  "log_id": "string",
  "seq": 456,
  "type": "checkpoint",
  "covers_seq": 455,
  "publications": [
    {
      "channel": "string",
      "evidence_type": "url|rfc3161_tsr|other",
      "evidence_ref": "string"
    }
  ],
  "pgp_sig": "string",
  "checkpoint_fp": "hex"
}
</pre>

<hr>

<h2>7. Glossary & Terminology</h2>
<dl>
  <dt><strong>Artifact</strong></dt>
  <dd>The item being committed (file, message, or state) in its raw form.</dd>
  
  <dt><strong>Canonical Bytes</strong></dt>
  <dd>The deterministic byte representation of an artifact, ensuring cross-platform hash consistency via RFC 8785 (JCS).</dd>

  <dt><strong>Checkpoint</strong></dt>
  <dd>Identification of a log head carrying signed publication evidence in an independently retrievable channel.</dd>

  <dt><strong>Divergence</strong></dt>
  <dd>A state where the calculated chain of fingerprints does not match the provided Root Hash, indicating tampering.</dd>

  <dt><strong>Fingerprint</strong></dt>
  <dd>A deterministic <strong>SHA-256</strong> digest of canonical bytes.</dd>

  <dt><strong>Link Continuity</strong></dt>
  <dd>The mandatory reference to the <code>record_fp</code> of the immediately preceding entry (seq-1).</dd>

  <dt><strong>Root Hash</strong></dt>
  <dd>The terminal <code>checkpoint_fp</code> representing the integrity of the entire dataset up to that sequence.</dd>
</dl>

<hr>

<h2>8. Mandatory Non-Claims</h2>
<p>Implementations MUST explicitly state they: <strong>do not verify correctness</strong>, <strong>do not certify compliance</strong>, <strong>do not adjudicate truth</strong>, and <strong>do not issue assets</strong>.</p>

<hr>

<p><strong>[End of RFC-ETIP-1.0]</strong></p>
