<h1>Epistemic Temporal Integrity Protocol (ETIP) v1.0</h1>
<p><strong>Category:</strong> Standards Track<br>
<strong>Maintainer:</strong> epistemic.center<br>
<strong>Status:</strong> RFC 1.0 (March 2026)</p>

<hr>

<h2>1. Positioning & Abstract</h2>
<p>The <strong>Epistemic Temporal Integrity Protocol (ETIP)</strong> specifies a standardized, tamper-evident record and verification protocol. It provides a mathematical guarantee that a digital record existed at a specific point in time and has remained unchanged since its creation.</p>
<ul>
  <li><strong>Non-Asset System:</strong> ETIP does not describe tokens or "minting" and is not an asset system.</li>
  <li><strong>Integrity vs. Truth:</strong> The protocol certifies <em>persistence</em> and <em>sequence</em>; it does not verify the "truthfulness" of the content.</li>
</ul>

<h2>2. Historical Context & Priors</h2>
<p>ETIP is a modern refinement of foundational concepts in cryptographic timestamping. We acknowledge the researchers whose work made this protocol possible:</p>
<ul>
  <li><strong>Haber & Stornetta (1991):</strong> For the invention of <strong>Hash Chaining</strong> as a solution for time-stamping digital documents without a trusted central party.</li>
  <li><strong>Daniel J. Bernstein (2011):</strong> For <strong>Ed25519</strong>, the high-performance Edwards-curve digital signature algorithm used for identity attestation.</li>
  <li><strong>RFC 8785 (JCS):</strong> For the <strong>JSON Canonicalization Scheme</strong>, ensuring deterministic byte representation across all platforms.</li>
</ul>

<hr>

<h2>3. Conformance Levels</h2>

<h3>3.1 ETIP-Compatible (Internal Integrity)</h3>
<p><strong>Requirements:</strong> Canonicalize artifacts, fingerprint with <strong>SHA-256</strong>, append sequentially, and maintain <strong>Link Continuity</strong>. 
<br><em>Time Semantics:</em> No time claims are permitted at this level; it serves strictly as an internal proof of sequence.</p>

<h3>3.2 ETIP-Conformant (Forensically Sealed)</h3>
<p><strong>Requirements:</strong> Satisfy 3.1, produce <strong>Checkpoints</strong> signed with <strong>Ed25519 PGP keys</strong>, and publish to at least <strong>two (2) Independent Witness Channels</strong>.
<br><em>Time Semantics:</em> Permits "No later than" claims for the ranges covered by the publication evidence.</p>

<hr>

<h2>4. Time Semantics & Epistemic Decay</h2>
<p>ETIP supports <strong>upper-bound time ("no later than")</strong> only for ranges covered by ETIP-Conformant checkpoints. ETIP does not prove exact creation time.</p>

<h3>4.1 Epistemic Decay (Informative)</h3>
<p>While cryptographic integrity does not expire, the "Epistemic Value" (relevance/trustworthiness) of a record may decay. Implementers SHOULD define <strong>Decay Horizons</strong> at the verification layer (e.g., records are considered stale after 400 days if not re-witnessed).</p>

<h2>5. Data Model (Schemas)</h2>

<h3>5.1 Commit Record</h3>
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

<h3>5.2 Checkpoint Record</h3>
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

<h2>6. Glossary & Terminology</h2>
<dl>
  <dt><strong>Artifact</strong></dt>
  <dd>The item being committed (file, message, or state) in its raw form.</dd>
  
  <dt><strong>Canonical Bytes</strong></dt>
  <dd>The deterministic byte representation of an artifact, ensuring cross-platform hash consistency via RFC 8785 (JCS).</dd>

  <dt><strong>Fingerprint</strong></dt>
  <dd>A deterministic <strong>SHA-256</strong> digest of canonical bytes.</dd>

  <dt><strong>Link Continuity</strong></dt>
  <dd>The mandatory reference to the <code>record_fp</code> of the immediately preceding entry (seq-1).</dd>

  <dt><strong>Checkpoint</strong></dt>
  <dd>The identification of a log head carrying publication evidence in an independently retrievable channel.</dd>

  <dt><strong>Independent Witness Channel</strong></dt>
  <dd>A channel (Class A, B, or C) whose administrative control is not held by the log operator.</dd>
  
  <dt><strong>Root Hash</strong></dt>
  <dd>The terminal <code>checkpoint_fp</code> representing the integrity of the entire dataset up to that sequence.</dd>
</dl>

<hr>

<h2>7. Mandatory Non-Claims</h2>
<p>Implementations MUST explicitly state they: <strong>do not verify correctness</strong>, <strong>do not certify compliance</strong>, <strong>do not adjudicate truth</strong>, and <strong>do not issue assets</strong>.</p>

<hr>

<p><strong>[End of RFC-ETIP-1.0]</strong></p>
