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
  <li>4. <a href="#conformance">Conformance Levels</a></li>
  <li>5. <a href="#infra">Infrastructure Requirements</a></li>
  <li>6. <a href="#witness">The Witnessing Rule (Classes A, B, C)</a></li>
  <li>7. <a href="#schemas">Data Model & Witness Schema</a></li>
  <li>8. <a href="#verification">Verification Algorithm</a></li>
  <li>9. <a href="#appendix">Appendix A: Reference Implementation</a></li>
</ul>

<hr />

<h2 id="license">0. License & Legal Shield</h2>
<p>This protocol is licensed under the <strong>Apache License, Version 2.0</strong>. Implementers are solely responsible for deployment security. No guarantee is provided regarding the future resilience of SHA-256 or Ed25519 against algorithmic advances.</p>

<h2 id="abstract">1. Abstract</h2>
<p><strong>ETIP-1.0</strong> defines a low-energy protocol for decentralized temporal data integrity. It utilizes <strong>Recursive SHA-256 Chaining</strong> and <strong>Identity-Linked Signatures</strong> to prevent history manipulation. The protocol is designed to operate within a <strong>10ms CPU cycle</strong>, ensuring that auditing capabilities function as a "rounding error" in global infrastructure costs.</p>

<h2 id="topology">2. System Topology</h2>
<pre>
[Operator Internal Log] ----> [Daily Root Hash] ----> [Independent Witness]
(Continuous Recording)       (h_roll_root)           (A, B, and/or C)
       |                            |                       |
       | 1. Append Records          | 2. Submit to Witness  | 3. Issue Receipt
       | 4. Start New Day <---------------------------------+
</pre>
<p>The topology ensures that internal activity is periodically "anchored" to external, independent evidence channels, converting subjective logs into objective forensic facts.</p>

<h2 id="chaining">3. Integrity Mechanism</h2>
<pre>
+---------------------+
| Previous h_roll     |-----\
+---------------------+      \      +---------------------+
                              +---->| SHA-256 Digest      |----> [Next h_roll]
+---------------------+      /      +---------------------+
| Current record_fp   |-----/
+---------------------+
</pre>
<p>Each record incorporates the cumulative state of all prior records. A single-byte change in history causes a total divergence in all subsequent <strong>h_roll</strong> values.</p>

<h2 id="conformance">4. Conformance Levels</h2>
<ul>
  <li><strong>ETIP-Compatible:</strong> Implementation of sequential hashing and basic <code>h_prev</code> linking.</li>
  <li><strong>ETIP-Conformant:</strong> Implementation of <strong>Ed25519 signatures</strong>, <strong>Recursive h_roll</strong>, and <strong>Multi-Class Witnessing</strong> (minimum two independent channels).</li>
</ul>

<h2 id="infra">5. Infrastructure Requirements</h2>
<ul>
  <li><strong>CPU Limit:</strong> Single-record processing MUST NOT exceed <strong>10ms</strong>.</li>
  <li><strong>Blob-First:</strong> Artifacts are processed as raw byte-streams to minimize serialization overhead.</li>
</ul>

<h2 id="witness">6. The Witnessing Rule (Classes A, B, C)</h2>
<p>Consistency is guaranteed through redundant external publication to three witness classes:</p>
<ul>
  <li><strong>Witness A (Public Utility):</strong> Neutral, high-availability service (e.g., public transparency log).</li>
  <li><strong>Witness B (Peer Witness):</strong> Mutual cross-witnessing between independent operators.</li>
  <li><strong>Witness C (TSA):</strong> RFC 3161 Time Stamping Authority providing legally recognized certificates.</li>
</ul>

<h2 id="schemas">7. Data Model & Witness Schema</h2>
<h3>7.1 Commit Record</h3>
<pre>
{
  "v": "1.0",
  "log_id": "string",
  "seq": 123,
  "h_art": "hex",           
  "h_roll": "hex",          
  "pub_key": "hex",         
  "sig": "hex",             
  "record_fp": "hex"        
}
</pre>

<h2 id="verification">8. Verification Algorithm</h2>
<ol>
  <li><strong>Recompute:</strong> Regenerate the hash chain from the last known-good state.</li>
  <li><strong>Verify:</strong> Confirm Ed25519 signatures for all records.</li>
  <li><strong>Divergence Check:</strong> Compare the final <strong>h_roll</strong> against Witness Receipts. Any mismatch indicates a <strong>Temporal Fork</strong>.</li>
</ol>

<h2 id="appendix">9. Appendix A: Reference Implementation</h2>
<p>Implementers SHOULD utilize hardware-accelerated SHA-256 instructions. A conforming implementation must be able to verify <strong>1,000 records in under 1 second</strong> of total CPU time.</p>

<hr />
<div align="center">
  <p><strong>[End of RFC-ETIP-1.0]</strong></p>
</div>
