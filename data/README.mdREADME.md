# Example Data (X, y) — Aviation Cybersecurity

This folder describes example (X, y) pairs that could be built from the three scientific articles analyzed on aviation cybersecurity, in the context of a supervised learning model.

## Context

The three articles analyzed address aviation cybersecurity from three distinct, complementary angles:

1. **Mizrak, F., & Akkartal, G. R. (2024).** *Prioritizing cybersecurity initiatives in aviation: A dematel-QSFS methodology.* Heliyon, 10(16), e35487.
2. **Dave, G., Choudhary, G., Sihag, V., You, I., & Choo, K.-K. R. (2022).** *Cyber security challenges in aviation communication, navigation, and surveillance.* Computers & Security, 112, 102516.
3. **AlMarri, M., Bahroun, Z., & Hassan, N. M. (2026).** *Artificial Intelligence for safety and resilience in airport Transportation Systems: A systematic review of Operational, Security, and environmental risks.* Transportation Research Interdisciplinary Perspectives, 37, 101961.

Below, a primary **integrated example** combines all three articles into a single model. This is followed by three **individual examples**, one per article, showing how each source could stand alone as a narrower supervised learning problem.

---

## Primary Example — Integrated Aviation Cybersecurity Risk Model

**Problem:** Predict the **cybersecurity risk level** of an aviation system or asset (e.g., a CNS subsystem, an airport, a connected aircraft), combining organizational governance (Article 1), technical CNS vulnerability (Article 2), and systemic airport risk context (Article 3).

### X (Features)

**From Article 1 — Strategic governance and priority (DEMATEL-QSFS):**
- `TDS` — Threat Detection Systems maturity (0–1)
- `DEP` — Data Encryption Protocols maturity (0–1)
- `RC` — Regulatory Compliance level (0–1)
- `IRP` — Incident Response Plans maturity (0–1)
- `UT` — User Training level (0–1)
- `ACM` — Access Control Mechanisms maturity (0–1)
- `NSS` — Network Security Solutions maturity (0–1)

> The authors identified `RC` and `TDS` as the most influential cause factors (cause weights 0.20 and 0.25); these should carry higher weight in the model, not be treated as neutral inputs.

**From Article 2 — Technical CNS vulnerability:**
- `subsystem` — `communication` (VHF/CPDLC), `navigation` (VOR/ILS/DME), `surveillance` (PSR/SSR/ADS-B)
- `attack_type` — `eavesdropping`, `jamming`, `flooding`, `injection`, `alteration`, `spoofing`
- `compromised_property` — `confidentiality`, `integrity`, `availability`

**From Article 3 — Systemic airport risk context:**
- `risk_cycle_stage` — `identification`, `assessment`, `mitigation`
- `domain_maturity` — methodological maturity of the risk domain (0–1); the article found security/cybersecurity risks less mature and less connected to predictive frameworks than operational/environmental risks

### y (Target)
**Cybersecurity risk level** — ordinal classification: `Low` / `Medium` / `High` / `Critical`

Reference calibration from real incidents cited in Article 1: the AASL breach (2024), the San Francisco International Airport ransomware attack (2020), the Heathrow DDoS attack (2015), and the Bristol Airport supply-chain attack (2018).

### Rationale
Article 3 explicitly notes that most studies treat risks in isolation, "resulting in fragmented insights," and proposes integrated risk-management ecosystems as a future research direction. This example follows that recommendation directly.

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
