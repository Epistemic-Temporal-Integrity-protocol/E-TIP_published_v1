<h1>Epistemic Temporal Integrity Protocol (ETIP) v1.1</h1>
<p>
<span class="badge">Standards Track</span>
<span class="badge">RFC 1.1</span>
<span class="badge">March 2026</span>
</p>

<div class="section">
<h2>1. Positioning & Intent</h2>
<p>
ETIP exists to solve a simple, stubborn problem: data can be quietly rewritten.
</p>
<p>
In systems like ESG reporting, numbers do not just exist — they evolve. What is often lost is not the latest value, but the history of how that value came to be.
</p>
<blockquote>
What did you say before — and did you change it later?
</blockquote>
<p>
ETIP is designed so that this question always has an answer.
</p>
<p>
It does not try to prove truth. It does not judge correctness. It does not enforce honesty.
</p>
<p>
Instead, it does something narrower, but more reliable:
</p>
<blockquote>
It makes it impossible to rewrite the past without leaving evidence.
</blockquote>
</div>

<div class="section">
<h2>2. Design Positioning</h2>
<p>
ETIP is a post-blockchain system, but more importantly, it is post-heavy infrastructure.
</p>
<p>
It keeps what mattered — chaining, fingerprinting, external witnessing — and removes what did not: consensus overhead, global coordination, and energy-intensive operation.
</p>
<blockquote>
ETIP takes what worked, and removes what didn’t.
</blockquote>
<p>
It is not designed for consensus. It is not designed for markets. It is not designed for scale at any cost.
</p>
<p>
It is designed for one thing only: preserving the integrity of data over time.
</p>
</div>

<div class="section">
<h2>3. Historical Lineage</h2>
<p>
ETIP stands on existing ideas rather than replacing them.
</p>
<ul>
<li>Hash chaining — linking records so the past cannot be changed silently</li>
<li>Merkle aggregation — compressing many records into a single proof</li>
<li>Public witnessing — anchoring data outside the control of its creator</li>
<li>Modern cryptographic primitives — fast, deterministic, widely available</li>
</ul>
<p>
These are not new inventions. ETIP simply combines them into a minimal, usable form.
</p>
</div>

<div class="section">
<h2>4. What ETIP Guarantees</h2>
<ul>
<li>History is append-only</li>
<li>Changes are visible</li>
<li>Past records cannot be silently altered</li>
<li>A record existed no later than when it was witnessed externally</li>
</ul>

<p><b>ETIP does not guarantee:</b></p>
<ul>
<li>That the data is correct</li>
<li>That all data was disclosed</li>
<li>That measurements were accurate</li>
</ul>
</div>

<div class="section">
<h2>5. Core Mechanism</h2>
<p>
Each record is reduced to a fingerprint.
</p>
<p>
Each fingerprint links to the previous one.
</p>
<p>
This creates a chain.
</p>
<p>
If any past record is changed, the chain breaks.
</p>
<p>
The chain is periodically published to independent witnesses.
</p>
<p>
Because those witnesses are outside the system, the past cannot be rewritten after publication.
</p>
</div>

<div class="section">
<h2>6. Time</h2>
<p>
ETIP does not prove exact time.
</p>
<blockquote>
It proves that something existed no later than when it was witnessed.
</blockquote>
<p>
This avoids trusting internal clocks, and instead relies on external history.
</p>
</div>

<div class="section">
<h2>7. Witnessing</h2>
<p>
A record is only meaningful if it is seen outside its own system.
</p>
<p>
ETIP requires independent witnesses.
</p>
<ul>
<li>Not controlled by the data owner</li>
<li>Publicly retrievable</li>
<li>Resistant to modification</li>
</ul>
<p>
No single system should be able to rewrite its own past.
</p>
</div>

<div class="section">
<h2>8. Infrastructure Philosophy</h2>
<p>
ETIP is designed to run in environments where resources are limited.
</p>
<p>
It does not assume large servers, continuous compute, or high energy availability.
</p>
<p>
It can run on edge workers, small devices, or low-power systems.
</p>
<p>
This is intentional.
</p>
<blockquote>
Integrity should not depend on heavy infrastructure.
</blockquote>
<p>
A system meant to improve accountability should not introduce unnecessary environmental cost.
</p>
</div>

<div class="section">
<h2>9. Data Model</h2>
<pre>{ "type": "commit", "prev": "...", "artifact": "..." }</pre>
<pre>{ "type": "checkpoint", "witness": [...] }</pre>
<pre>{ "supersedes": "previous" }</pre>
</div>

<div class="section">
<h2>10. Threat Model</h2>
<ul>
<li>Prevents silent rewriting of history</li>
<li>Prevents unnoticed deletion</li>
<li>Does not prevent false input</li>
<li>Does not prevent omission</li>
</ul>
</div>

<div class="section">
<h2>11. ESG Context</h2>
<p>
ETIP does not stop companies from changing numbers.
</p>
<p>
It makes those changes visible.
</p>
<p>
Auditors no longer look only at the latest value.
</p>
<p>
They look at how the value changed over time.
</p>
</div>

<div class="section">
<h2>12. Mandatory Non-Claims</h2>
<p>
ETIP does not verify truth.
</p>
<p>
It does not certify correctness.
</p>
<p>
It does not enforce compliance.
</p>
<p>
It does not ensure completeness.
</p>
<p>
It does not issue assets or tokens.
</p>
<p>
It only preserves history.
</p>
</div>

<div class="section">
<h2>13. Changelog</h2>
<ul>
<li>Clarified "no later than" time guarantee</li>
<li>Added explicit guarantees vs non-guarantees</li>
<li>Strengthened witness definition</li>
<li>Added threat model explanation</li>
<li>Introduced optional revision linking</li>
<li>Refocused language on ESG audit use-case</li>
<li>Restored narrative intent</li>
<li>Clarified post-blockchain and post-infrastructure positioning</li>
<li>Simplified language for broader accessibility</li>
</ul>
</div>

</body>
</html>

