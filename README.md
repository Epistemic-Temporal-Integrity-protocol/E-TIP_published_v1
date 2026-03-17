<div align="center">
  <h1>Epistemic Temporal Integrity Protocol (ETIP) v1.0</h1>
  <p><strong>Request for Comments: 1.0</strong></p>
  <p>Category: Standards Track | Status: Final | March 2026</p>
</div>

<hr />

<div style="border: 2px solid #000; padding: 15px; background: #f0f0f0; font-weight: bold; margin-bottom: 20px;">
  LEGAL NOTICE: THIS PROTOCOL IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND. IN NO EVENT SHALL THE AUTHORS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE PROTOCOL OR THE USE OR OTHER DEALINGS IN THE PROTOCOL.
</div>

<h3>Table of Contents</h3>
<ul>
  <li>0. <a href="#license">License & Legal Shield</a></li>
  <li>1. <a href="#abstract">Abstract</a></li>
  <li>2. <a href="#topology">System Topology</a></li>
  <li>3. <a href="#integrity">Integrity Mechanism</a></li>
  <li>4. <a href="#conformance">Conformance Levels</a></li>
  <li>5. <a href="#infra">Infrastructure Requirements</a></li>
  <li>6. <a href="#witness">The Witnessing Rule (Classes A, B, C)</a></li>
  <li>7. <a href="#data-model">Data Model & Witness Schema</a></li>
  <li>8. <a href="#verification">Verification Algorithm</a></li>
  <li>9. <a href="#appendix">Appendix A: Reference Implementation Logic</a></li>
</ul>

<hr />

<h2 id="license">0. License & Legal Shield</h2>
<p>This protocol specification is licensed under the <strong>Apache License, Version 2.0</strong>. Implementers are solely responsible for deployment security and cryptographic key management.</p>

<h2 id="abstract">1. Abstract</h2>
<p>ETIP-1.0 defines a decentralized mechanism for temporal data integrity. It utilizes <strong>Recursive SHA-256 Chaining</strong> and <strong>Identity-Linked Ed25519 Signatures</strong> to establish an immutable timeline. The protocol is optimized for execution within a <strong>10ms CPU cycle</strong>, ensuring integrity verification functions as a nominal cost in global infrastructure.</p>

<h2 id="topology">2. System Topology</h2>
<pre>
[Internal Log] --------> [Cumulative State] --------> [External Witness]
(High-Frequency)         (h_roll_root)                (Verification Anchor)
       |                        |                            |
       | 1. Record Commit       | 2. Daily Export            | 3. Anchor Hash
       | 4. Next Day Link <----------------------------------+
</pre>

<h2 id="integrity">3. Integrity Mechanism</h2>
<p>Each record incorporates the cumulative state of all prior records via recursive hashing. Any modification of historical data results in a complete divergence of the current <strong>Rolling Hash (h_roll)</strong>.</p>
<pre>
h_roll[n] = SHA256( h_roll[n-1] + record_fp[n] )
</pre>

<h2 id="conformance">4. Conformance Levels</h2>
<ul>
  <li><strong>ETIP-Compatible:</strong> Implements sequential hashing and prev-linking.</li>
  <li><strong>ETIP-Conformant:</strong> Implements Ed25519 signatures, recursive h_roll, and mandatory multi-class external witnessing.</li>
</ul>

<h2 id="infra">5. Infrastructure Requirements</h2>
<ul>
  <li><strong>Computational Efficiency:</strong> Processing MUST NOT exceed 10ms per record.</li>
  <li><strong>Data Handling:</strong> Protocol operates on raw byte-streams (Blob-First) to minimize serialization overhead.</li>
</ul>

<h2 id="witness">6. The Witnessing Rule (Classes A, B, C)</h2>
<p>To establish objective truth, the final state hash MUST be published to independent channels:</p>
<ul>
  <li><strong>Class A (Public Utility):</strong> High-availability, neutral publication service.</li>
  <li><strong>Class B (Peer Witness):</strong> Cross-witnessing between independent operators.</li>
  <li><strong>Class C (TSA):</strong> RFC 3161 Time Stamping Authority for certified temporal evidence.</li>
</ul>

<h2 id="data-model">7. Data Model & Witness Schema</h2>

<h4>7.1 Commit Record (Internal)</h4>
<pre>
{
  "v": "1.0",
  "log_id": "uuid",
  "seq": "uint64",
  "h_art": "hex",           // SHA-256(Artifact)
  "h_roll": "hex",          // Recursive State Hash
  "pub_key": "hex",         // Ed25519 Public Key
  "sig": "hex",             // Signature of h_roll
  "record_fp": "hex"        // SHA-256 of Record Object
}
</pre>

<h4>7.2 Witness Receipt (External)</h4>
<pre>
{
  "v": "1.0",
  "type": "witness_receipt",
  "timestamp": "unix_ts",
  "h_roll_root": "hex",
  "witness_sig": "hex",
  "witness_ref": "uri"
}
</pre>

<h4>7.3 Continuity Record (Initialization)</h4>
<p>The first record of a new epoch MUST embed the previous epoch's Witness Receipt to ensure continuity.</p>

<h2 id="verification">8. Verification Algorithm</h2>
<ol>
  <li>Recompute the SHA-256 chain from a known-good state.</li>
  <li>Verify Ed25519 signatures for attribution.</li>
  <li>Match the final cumulative hash against published Witness Receipts from Class A, B, or C.</li>
</ol>

<h2 id="appendix">9. Appendix A: Reference Implementation Logic</h2>
<p>Implementations MUST support hardware-accelerated SHA-256. A conforming environment MUST be capable of verifying 1,000 records per second.</p>

<hr />
<div align="center">
  <p><strong>[End of RFC-ETIP-1.0]</strong></p>
</div>
