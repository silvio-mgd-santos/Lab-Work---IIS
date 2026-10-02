# Example Data (X, y) — Aviation Cybersecurity

This folder describes example (X, y) pairs that could be built from the three scientific articles analyzed on aviation cybersecurity, in the context of a supervised learning model.

## Context

The three articles analyzed address aviation cybersecurity from three distinct, complementary angles:

1. **Mizrak, F., & Akkartal, G. R. (2024).** *Prioritizing cybersecurity initiatives in aviation: A dematel-QSFS methodology.* Heliyon, 10(16), e35487.
2. **Dave, G., Choudhary, G., Sihag, V., You, I., & Choo, K.-K. R. (2022).** *Cyber security challenges in aviation communication, navigation, and surveillance.* Computers & Security, 112, 102516.
3. **AlMarri, M., Bahroun, Z., & Hassan, N. M. (2026).** *Artificial Intelligence for safety and resilience in airport Transportation Systems: A systematic review of Operational, Security, and environmental risks.* Transportation Research Interdisciplinary Perspectives, 37, 101961.

Below, a primary **simplified example** uses features from Article 1 with a clear threshold rule. This is followed by three **individual examples**, one per article, showing how each source could define its own narrower supervised learning problem.

---

## Primary Example — Aviation Cybersecurity Risk Classification (Simplified)

**Problem:** Predict whether an aviation organization's cybersecurity posture represents a `Low` or `High` risk, based on the maturity of its governance practices.

### X (Features)

The seven criteria defined and empirically weighted in Article 1, each scored on a 0–1 maturity scale:
- `TDS` — Threat Detection Systems maturity
- `DEP` — Data Encryption Protocols maturity
- `RC` — Regulatory Compliance level
- `IRP` — Incident Response Plans maturity
- `UT` — User Training level
- `ACM` — Access Control Mechanisms maturity
- `NSS` — Network Security Solutions maturity

A derived feature, `avg_score`, is computed as the simple average of the seven criteria above.

### y (Target)

**Cybersecurity risk level** — binary classification: `Low` / `High`

**Rule:** if `avg_score >= 0.5`, the system is classified as `Low` risk (mature security posture); if `avg_score < 0.5`, it is classified as `High` risk (immature security posture).

### Why only Article 1's features, but all three articles matter

This simplified version uses only the governance maturity criteria from Article 1 to keep the dataset easy to read and the classification rule transparent. However, Articles 2 and 3 justify *why* these criteria matter in practice:
- **Article 2** shows concretely how weak threat detection and encryption translate into exploitable technical vulnerabilities in CNS protocols (e.g., unauthenticated ADS-B and CPDLC messages being susceptible to spoofing, jamming, and injection attacks).
- **Article 3** shows that security-related risks at airports are systemically less mature and less integrated into predictive safety frameworks than operational or environmental risks — reinforcing why low governance maturity (as captured by `avg_score`) is a meaningful proxy for elevated real-world risk.

So while the dataset columns come from Article 1, the choice of criteria and the interpretation of the `Low`/`High` threshold are grounded in findings from all three articles.

---

## Additional Example A — Based on Article 1 (Strategic Prioritization)

**Problem:** Predict which cybersecurity initiative an aviation organization should prioritize.

**X:** Expert-assigned fuzzy scores (via QSFS) for the seven DEMATEL criteria — `TDS`, `DEP`, `RC`, `IRP`, `UT`, `ACM`, `NSS` — each on a 0–1 influence/interdependence scale.

**y:** Strategic priority level of the initiative — `Low` / `Medium` / `High` / `Critical`

**Rationale:** This mirrors the article's own DEMATEL-QSFS decision logic directly — a supervised model would learn to predict the priority ranking that the method assigns, given new sets of expert scores.

## Additional Example B — Based on Article 2 (CNS Attack Detection)

**Problem:** Detect and classify an attack on a CNS system in real time.

**X:** Signal strength (RSSI), ADS-B position deviation between consecutive messages, timestamp consistency (drift), number of independent ground receivers confirming the same signal, message frequency.

**y:** Attack class — `Benign` / `GPS Spoofing` / `ADS-B Injection` / `Jamming` / `DoS` (multi-class classification)

**Rationale:** The article documents SDR-based (software-defined radio) attacks targeting popular wireless technologies; this example models exactly the kind of real-time detection an aviation intrusion detection system (IDS) would need to perform.

## Additional Example C — Based on Article 3 (Airport Risk Domain Classification)

**Problem:** Predict the dominant risk domain for a given airport process or system.

**X:** System/process type (apron, baggage systems, local ATC), daily traffic volume, weather severity, number of remote/connected (IoT) access points, incident history over the past 12 months.

**y:** Predicted dominant risk domain — `Operational` / `Security` / `Environmental` / `Occupational` / `Human` (multi-class classification)

**Rationale:** This aligns with the article's core objective — using AI to support the risk-management cycle (identification → assessment → mitigation) — specifically modeling the hazard identification/classification stage.

---

## Files in this directory

- `README.md` — this file
- `examples.csv` — sample data rows for the primary (simplified) example above
