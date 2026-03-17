<div align="center">
  <h1>Epistemic Temporal Integrity Protocol (ETIP) v1.0</h1>
  <p><strong>Request for Comments: 1.0</strong></p>
  <p>epistemic.center | Category: Standards Track | March 2026</p>
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
  <li>6. <a href="#witness">The Witnessing Rule (Redundancy Combinations)</a></li>
  <li>7. <a href="#data-model">Data Model & Witness Schema</a></li>
  <li>8. <a href="#verification">Verification Algorithm</a></li>
  <li>9. <a href="#appendix">Appendix A: Reference Implementation Logic</a></li>
</ul>

<hr />

<h2 id="license">0. License & Legal Shield</h2>
<p>This protocol is licensed under the <strong>Apache License, Version 2.0</strong>. Implementers are solely responsible for deployment security and cryptographic key management.</p>

<h2 id="abstract">1. Abstract</h2>
<p>ETIP-1.0 defines a decentralized mechanism for temporal data integrity. It utilizes <b>Recursive SHA-256 Chaining</b> and <b>Identity-Linked Ed25519 Signatures</b>. The protocol is optimized for a <b>10ms CPU cycle</b>, ensuring integrity functions as a nominal cost in global infrastructure.</p>

<h2 id="topology">2. System Topology</h2>
<pre>
[Internal Log] --------> [Cumulative State] --------> [External Witness]
(High-Frequency)         (h_roll_root)                (Verification Anchor)
       |                        |                            |
       | 1. Record Commit       | 2. Daily Export            | 3. Anchor Hash
       | 4. Next Day Link <----------------------------------+
</pre>

<h2 id="integrity">3. Integrity Mechanism</h2>
<p>Each record incorporates the cumulative state of all prior records. Modification of historical data results in a complete divergence of the current <b>Rolling Hash (h_roll)</b>.</p>
<pre>h_roll[n] = SHA256( h_roll[n-1] + record_fp[n] )</pre>

<h2 id="conformance">4. Conformance Levels</h2>
<ul>
  <li><b>ETIP-Compatible:</b> Sequential hashing and prev-linking.</li>
  <li><b>ETIP-Conformant:</b> Ed25519 signatures, recursive h_roll, and mandatory multi-class external witnessing.</li>
</ul>

<h2 id="infra">5. Infrastructure Requirements</h2>
<ul>
  <li><b>CPU Budget:</b> Processing MUST NOT exceed 10ms per record.</li>
  <li><b>Format:</b> Protocol operates on raw byte-streams (Blob-First).</li>
</ul>

<h2 id="witness">6. The Witnessing Rule & Redundancy</h2>
<p>Final state hashes MUST be published to independent channels to achieve objective consensus.</p>
<h4>6.1 Valid Witness Combinations</h4>
<p>A log is considered <b>Forensically Sealed</b> only if it meets one of these combinations:</p>
<ul>
  <li><b>Class A + Class B:</b> Public Utility + Independent Peer.</li>
  <li><b>Class B + Class B:</b> Two independent Peer Witnesses.</li>
  <li><b>Class A/B + Class C:</b> Either Utility or Peer + RFC 3161 TSA Certificate.</li>
</ul>

<h2 id="data-model">7. Data Model & Witness Schema</h2>
<pre>
{
  "v": "1.0",
  "log_id": "uuid",
  "seq": "uint64",
  "h_art": "hex",           // SHA-256(Artifact)
  "h_roll": "hex",          // Recursive State Hash
  "pub_key": "hex",         // Ed25519 Public Key
  "sig": "hex",             // Signature of h_roll
  "record_fp": "hex"        // SHA-256 of all fields EXCEPT record_fp
}
</pre>

<h2 id="verification">8. Verification Algorithm</h2>
<ol>
  <li>Retrieve initial state (<code>h_roll_0</code>) from a Witness Receipt.</li>
  <li>For each record, re-calculate <code>record_fp</code> and then <code>h_roll[n]</code>.</li>
  <li>Verify Ed25519 signature of <code>h_roll[n]</code> matches <code>pub_key</code>.</li>
  <li>Match the terminal <code>h_roll[end]</code> against the target Witness Receipt.</li>
</ol>

<h2 id="appendix">9. Appendix A: Reference Implementation Logic</h2>

<h4>A.1 The Commit Loop (Pseudo-code)</h4>
<pre>
Function Commit(artifact, log_state):
    1.  h_art = SHA256(artifact)
    2.  seq = log_state.next_seq++
    3.  // Construct Record
        payload = { v: "1.0", log_id: log_state.id, seq: seq, h_art: h_art, pub_key: log_state.pub_key }
    4.  record_fp = SHA256(payload)
    5.  // Chaining
        h_roll = SHA256(log_state.last_h_roll + record_fp)
    6.  // Attribution
        sig = Ed25519_Sign(log_state.private_key, h_roll)
    7.  Save Record { ...payload, h_roll, sig, record_fp }
    8.  log_state.last_h_roll = h_roll
</pre>

<h4>A.2 Divergence Detection (The Audit)</h4>
<pre>
Function Verify(record_n, h_roll_prev):
    1.  // Verify Record Integrity
        calc_fp = SHA256(record_n fields minus record_fp)
        IF calc_fp != record_n.record_fp: RETURN "Data Corrupted"
    2.  // Verify Chain Continuity
        calc_roll = SHA256(h_roll_prev + record_n.record_fp)
        IF calc_roll != record_n.h_roll: RETURN "Chain Divergence"
    3.  // Verify Attribution
        IF !Ed25519_Verify(record_n.pub_key, record_n.sig, record_n.h_roll):
            RETURN "Signature Invalid"
    RETURN "Valid"
</pre>

<hr />
<div align="center">
  <p><strong>[End of RFC-ETIP-1.0]</strong></p>
</div>
