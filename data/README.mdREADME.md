# Example Data (X, y) — Aviation Cybersecurity

This folder describes (X, y) example that could be built from the three scientific articles analyzed on aviation cybersecurity, in the context of a supervised learning model.

## Context

The three articles analyzed address aviation cybersecurity from three distinct, complementary angles:

1. **Mizrak, F., & Akkartal, G. R. (2024).** *Prioritizing cybersecurity initiatives in aviation: A dematel-QSFS methodology.* Heliyon, 10(16), e35487.
2. **Dave, G., Choudhary, G., Sihag, V., You, I., & Choo, K.-K. R. (2022).** *Cyber security challenges in aviation communication, navigation, and surveillance.* Computers & Security, 112, 102516.
3. **AlMarri, M., Bahroun, Z., & Hassan, N. M. (2026).** *Artificial Intelligence for safety and resilience in airport Transportation Systems: A systematic review of Operational, Security, and environmental risks.* Transportation Research Interdisciplinary Perspectives, 37, 101961.

A model built from a single article would only capture one dimension of the problem (governance, technical detection, or systemic context). Article 3 explicitly identifies this fragmentation as a literature gap, recommending the development of "Integrated Risk-Management Ecosystems" where detection, assessment, and mitigation function as a connected system. The (X, y) example below integrates the three sources with that goal in mind.

## Problem

Predict the **cybersecurity risk level** of an aviation system or asset (e.g., a CNS subsystem, an airport, a connected aircraft), combining:
- the **organizational governance** dimension (Article 1)
- the **technical vulnerability of CNS protocols** dimension (Article 2)
- the **systemic airport risk context** dimension (Article 3)

## X (Features)

### From Article 1 — Strategic governance and priority (DEMATEL-QSFS)
The seven criteria used by the authors, each scored on a 0–1 scale:
- `TDS` — Threat Detection Systems (maturity of threat detection systems)
- `DEP` — Data Encryption Protocols (maturity of encryption protocols)
- `RC` — Regulatory Compliance (level of regulatory compliance)
- `IRP` — Incident Response Plans (maturity of incident response plans)
- `UT` — User Training (level of user training)
- `ACM` — Access Control Mechanisms (maturity of access controls)
- `NSS` — Network Security Solutions (maturity of network security solutions)

> Note: the authors identified `RC` and `TDS` as the most influential cause factors (cause weights of 0.20 and 0.25), and `UT`/`DEP` as the most impacted effect factors. In the model, `RC` and `TDS` should be treated as drivers (higher weight), not as neutral variables.

### From Article 2 — Technical vulnerability of CNS systems
- `subsystem` — category of the system involved: `communication` (VHF/CPDLC), `navigation` (VOR/ILS/DME), `surveillance` (PSR/SSR/ADS-B)
- `attack_type` — type of attack observed, per the article's taxonomy: `eavesdropping`, `jamming`, `flooding`, `injection`, `alteration`, `spoofing`
- `compromised_property` — which security property is at risk: `confidentiality`, `integrity`, `availability`

> Note: protocols such as ADS-B and CPDLC lack native authentication, making them vulnerable to most of these attack types simultaneously.

### From Article 3 — Systemic airport risk context
- `risk_cycle_stage` — stage of the risk management cycle: `identification`, `assessment`, `mitigation`
- `domain_maturity` — indicator of the domain's methodological maturity (0–1), reflecting the article's finding that security/cybersecurity risks are less mature and less connected to predictive frameworks than operational and environmental risks

## y (Target)

**Cybersecurity risk level** — ordinal classification in 4 classes:
- `Low`
- `Medium`
- `High`
- `Critical`

Reference calibration based on real incidents cited in Article 1:
- Airport and Aviation Services Sri Lanka (AASL) breach, 2024 — exposure of 7,000+ records
- Ransomware at San Francisco International Airport, 2020
- DDoS attack on Heathrow Airport, 2015
- Supply-chain attack on Bristol Airport, 2018

## Rationale for integration

A single unified model (rather than three separate models) directly reflects an explicit recommendation from the reviewed literature: Article 3 notes that most studies treat risks in isolation, "resulting in fragmented insights," and proposes as a future research agenda the development of integrated risk-management ecosystems where detection, assessment, and mitigation function as one connected system. This (X, y) example follows that logic: it brings together strategic governance (Article 1), real-time technical detection (Article 2), and the systemic context of airport risk (Article 3) into a single classification problem.
