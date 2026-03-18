<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
</head>
<body>

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
In many real-world systems—especially ESG reporting—numbers change over time. Sometimes legitimately. Sometimes conveniently. What is often missing is not the data itself, but a reliable way to answer a more important question:
</p>
<blockquote>
What did you say before—and did you change it later?
</blockquote>
<p>
ETIP does not attempt to prove whether data is true. It does not judge correctness, quality, or completeness.
</p>
<p>
Instead, it enforces something more fundamental:
</p>
<blockquote>
Once data is recorded, it cannot be silently rewritten without leaving evidence.
</blockquote>
</div>

<div class="section">
<h2>2. What ETIP Guarantees (and What It Does Not)</h2>
<p><b>ETIP guarantees:</b></p>
<ul>
<li>Records are append-only (history cannot be rewritten invisibly)</li>
<li>Changes to data become visible as new entries</li>
<li>A record existed no later than a verifiable external publication</li>
<li>The sequence of records has not been tampered with</li>
</ul>

<p><b>ETIP does NOT guarantee:</b></p>
<ul>
<li>That data is correct</li>
<li>That all data has been disclosed</li>
<li>That measurements were accurate</li>
<li>That actors behaved honestly</li>
</ul>

<p>
This distinction is intentional. ETIP is an integrity protocol, not a truth engine.
</p>
</div>

<div class="section">
<h2>3. Core Idea (Plain Language)</h2>
<p>
Each piece of data is converted into a unique fingerprint. That fingerprint depends on the exact contents of the data.
</p>
<p>
Each new record links to the one before it. This creates a chain.
</p>
<p>
If any past record is changed, the chain breaks.
</p>
<p>
Periodically, the latest state of the chain is published to independent external systems. These act as witnesses.
</p>
<p>
Because these witnesses are outside the control of the data owner, the history cannot be secretly rewritten after publication.
</p>
</div>

<div class="section">
<h2>4. Time Semantics</h2>
<p>
ETIP does not prove when something was created.
</p>
<p>
It proves something weaker—but reliable:
</p>
<blockquote>
A record existed no later than the moment it was independently witnessed.
</blockquote>
<p>
This avoids reliance on internal clocks, which can be manipulated, and instead relies on external publication that cannot be rewritten.
</p>
</div>

<div class="section">
<h2>5. The Rule of Two (Witnessing)</h2>
<p>
For a record to be considered forensically sealed, it must be published to at least two independent witness channels.
</p>
<p>
A valid witness should be:</p>
<ul>
<li>Externally controlled (not owned by the data publisher)</li>
<li>Publicly retrievable</li>
<li>Append-only or resistant to modification</li>
</ul>
<p>
This ensures no single party can rewrite history unnoticed.
</p>
</div>

<div class="section">
<h2>6. Data Model (Simplified)</h2>
<h3>Commit Record</h3>
<pre>{
  "spec": "ETIP-1.1",
  "seq": 123,
  "type": "commit",
  "prev_record_fp": "...",
  "artifact_fp": "...",
  "record_fp": "..."
}</pre>

<h3>Checkpoint Record</h3>
<pre>{
  "spec": "ETIP-1.1",
  "seq": 456,
  "type": "checkpoint",
  "covers_seq": 455,
  "publications": [...],
  "checkpoint_fp": "..."
}</pre>

<p>
Each record contains a fingerprint of itself. This makes tampering immediately detectable.
</p>

<h3>Optional: Revision Linking</h3>
<pre>{
  "supersedes": "previous_record_fp"
}</pre>
<p>
This allows explicit tracking of corrections without altering past data.
</p>
</div>

<div class="section">
<h2>7. Threat Model (Reality Check)</h2>
<p>
ETIP is designed for environments where actors may have incentives to improve how their past data appears.
</p>
<p>
It protects against:</p>
<ul>
<li>Silent rewriting of historical records</li>
<li>Deletion of inconvenient past data</li>
<li>Undetected modification of prior disclosures</li>
</ul>

<p>
It does not protect against:</p>
<ul>
<li>False data being entered initially</li>
<li>Data being withheld entirely</li>
<li>Delaying publication before checkpointing</li>
</ul>
</div>

<div class="section">
<h2>8. Why This Matters (ESG Context)</h2>
<p>
In ESG reporting, the most common issue is not blatant fraud, but quiet revision.
</p>
<p>
Numbers improve over time, but the history of those changes is often unclear or lost.
</p>
<p>
ETIP changes this dynamic:
</p>
<ul>
<li>Original disclosures remain visible</li>
<li>Revisions become explicit events</li>
<li>Auditors can evaluate the timeline, not just the latest state</li>
</ul>
<p>
This shifts accountability from what is reported now to how reporting evolves over time.
</p>
</div>

<div class="section">
<h2>9. Non-Claims</h2>
<ul>
<li>ETIP does not verify correctness</li>
<li>ETIP does not enforce compliance</li>
<li>ETIP does not issue tokens or assets</li>
<li>ETIP does not replace auditing</li>
</ul>
</div>

<div class="section">
<h2>10. Changelog</h2>
<h3>v1.1</h3>
<ul>
<li>Clarified "no later than" time guarantee</li>
<li>Added explicit guarantees vs non-guarantees</li>
<li>Strengthened witness definition</li>
<li>Added threat model explanation</li>
<li>Introduced optional revision linking</li>
<li>Refocused language on ESG audit use-case</li>
</ul>
</div>

</body>
</html>
