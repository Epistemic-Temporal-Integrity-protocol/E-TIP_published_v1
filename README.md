<div align="center">
  <h1>Epistemic Temporal Integrity Protocol (ETIP) v1.0</h1>
  <p><strong>Request for Comments: 1.0</strong></p>
  <p>epistemic.center | Category: Standards Track | March 2026</p>
</div>

<hr />

<blockquote>
  <strong>LEGAL NOTICE:</strong> THIS PROTOCOL IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND. IN NO EVENT SHALL THE AUTHORS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE PROTOCOL OR THE USE OR OTHER DEALINGS IN THE PROTOCOL.
</blockquote>

<h3>Table of Contents</h3>
<ul>
  <li>0. <a href="#license">License & Legal Shield</a></li>
  <li>1. <a href="#abstract">Abstract</a></li>
  <li>2. <a href="#topology">System Topology</a></li>
  <li>3. <a href="#chaining">Integrity Mechanism</a></li>
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
