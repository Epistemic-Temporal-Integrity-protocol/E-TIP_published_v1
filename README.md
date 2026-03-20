<div align="center" style="background-color: #fff3cd; color: #856404; padding: 24px; border: 1px solid #ffeeba; border-radius: 8px; margin-bottom: 32px; font-family: sans-serif;">
  <h1 style="margin: 0; font-size: 1.5rem;">Official Specification: <a href="https://epistemic.center" style="color: #0052cc; text-decoration: underline;">epistemic.center</a></h1>
  <p style="margin: 10px 0 0 0; font-size: 1rem;">This GitHub repository serves as a <b>draft versioning node</b> only.</p>
  <p style="margin: 5px 0 0 0; font-size: 0.9rem; opacity: 0.8;">Implementations: <a href="https://carbonprofile.world" style="color: #0052cc;">CarbonProfile.world</a> | <a href="https://epistemic.stream" style="color: #0052cc;">Mycelium</a></p>
</div>

Epistemic Temporal Integrity Protocol (ETIP) v1.1
<div style="margin-bottom: 20px;">
<img src="https://img.shields.io/badge/Version-1.1-blue" alt="Version 1.1">
<img src="https://img.shields.io/badge/Status-Draft-orange" alt="Status">
<img src="https://img.shields.io/badge/Focus-ESG_Integrity-green" alt="Focus">
</div>

Drafted by Y. Chen | LinkedIn Profile

1. Positioning & Intent
ETIP exists to solve a simple, stubborn problem: data can be quietly rewritten.

In systems like ESG reporting, numbers do not just exist — they evolve. What is often lost is not the latest value, but the history of how that value came to be.

"What did you say before — and did you change it later?"

ETIP is designed so that this question always has an answer. It does not try to prove truth or judge correctness; it makes it impossible to rewrite the past without leaving evidence.

2. Design Positioning
ETIP is a post-blockchain system, and also post-heavy infrastructure. It keeps what mattered — chaining, fingerprinting, external witnessing — and removes what did not: consensus overhead, global coordination, and energy-intensive operation.

3. Historical Lineage
ETIP stands on established industry standards:

Hash chaining — linking records for immutable sequencing.

Merkle aggregation — compressing many records into a single proof.

Public witnessing — anchoring data outside creator control.

SHA-256 — collision-resistant fingerprinting standard.

Ed25519 — efficient, verifiable digital signatures.

JSON Canonicalization (RFC 8785) — deterministic data representation.

4. What ETIP Guarantees
History is append-only.

Changes are visible.

Past records cannot be silently altered.

A record existed no later than when it was witnessed externally.

5. Core Mechanism
Each record is first converted into canonical form (RFC 8785), reduced to a SHA-256 fingerprint, and linked to the previous hash. The chain is periodically signed using Ed25519 and published to independent witnesses.

6. Levels of Use
ETIP-Compatible: Records are chained internally. Provides integrity of sequence.

ETIP-Conformant: The chain is signed and published to independent witnesses.

7. Time
ETIP does not prove exact time. It proves that something existed no later than when it was witnessed.

8. Witnessing
At least one Root Hash MUST be published within every 24-hour window to be considered forensically sealed.

Witness Classes
Class A — Public Service: Independent, publicly accessible (e.g., public ledgers).

Class B — Conforming Participants: Peer companies, NGOs, or industry groups.

Class C — Timestamp Authorities: Certified time-stamping services.

9. Infrastructure Philosophy
ETIP is designed to run in constrained environments. Integrity should not depend on heavy infrastructure or increase environmental costs.

10. Data Model
JSON
{
  "type": "commit",
  "prev": "sha256",
  "artifact": "sha256",
  "record_fp": "sha256"
}
11. Threat Model
Prevents silent rewriting of history and unnoticed deletion.

Does not prevent false input or intentional omission.

12. ESG Context
ETIP makes changes visible. Auditors evaluate timelines, not snapshots.

13. Mandatory Non-Claims
ETIP does not verify truth, certify correctness, or enforce compliance.

ETIP only preserves history.

Draft Integrity Seal
To verify the integrity of this README, use the following PowerShell command:
Get-FileHash README.md -Algorithm SHA256

© 2026 Y. Chen. All rights reserved. Visit epistemic.center for the authoritative version.
