# Prompt 2 – Few-shot

The same task as Prompt 1, with 5 known (X, y) pairs from Lab 1.2 (`data/examples.csv`) placed before the new observation.

## Prompt

```text
You are an AI agent that works as an end-to-end aviation cybersecurity risk classifier.

TASK
You receive a text description X of an aviation organisation (for example an airport).
Run these stages and return the final result y.

STAGE 1 - DATA UNDERSTANDING
Read X and find evidence for each of these 7 criteria:
TDS (threat detection), DEP (data encryption/protection), RC (regulatory compliance),
IRP (incident response plan), UT (user training), ACM (access control management),
NSS (network security). If X says nothing about a criterion, say so and give it 0.3.

STAGE 2 - INFERENCE
Give each criterion a maturity score from 0.0 (absent) to 1.0 (excellent).

STAGE 3 - REASONING
Compute avg_score = mean of the 7 scores.
Decision rule: avg_score >= 0.5 -> "Low" risk; avg_score < 0.5 -> "High" risk.
Identify the 2 weakest criteria.

STAGE 4 - OUTPUT GENERATION
Return ONLY valid JSON with this format:
{"scores": {...7 criteria...}, "avg_score": 0.00, "risk_level": "Low or High",
 "weakest_criteria": ["", ""], "explanation": "...", "recommendations": ["...", "...", "..."]}

KNOWN EXAMPLES (X -> y)

Example 1
X: Airport with mature threat detection, strong encryption, full regulatory compliance, tested incident response plans, regular user training, strict access control, and solid network security.
y: {"scores": {"TDS": 0.8, "DEP": 0.7, "RC": 0.9, "IRP": 0.7, "UT": 0.6, "ACM": 0.7, "NSS": 0.6}, "avg_score": 0.71, "risk_level": "Low"}

Example 2
X: Airport with moderate threat detection, partial encryption coverage, inconsistent regulatory compliance, outdated incident response plans, limited user training, weak access control, and basic network security.
y: {"scores": {"TDS": 0.4, "DEP": 0.3, "RC": 0.5, "IRP": 0.4, "UT": 0.3, "ACM": 0.4, "NSS": 0.3}, "avg_score": 0.37, "risk_level": "High"}

Example 3
X: Airport with almost no threat detection, no encryption, failing regulatory compliance, no incident response plan, no user training, no access control, and no network security measures.
y: {"scores": {"TDS": 0.1, "DEP": 0.1, "RC": 0.2, "IRP": 0.1, "UT": 0.1, "ACM": 0.1, "NSS": 0.1}, "avg_score": 0.11, "risk_level": "High"}

Example 4
X: Airport with average threat detection, average encryption, average regulatory compliance, average incident response readiness, average user training, average access control, and average network security.
y: {"scores": {"TDS": 0.5, "DEP": 0.5, "RC": 0.4, "IRP": 0.5, "UT": 0.4, "ACM": 0.5, "NSS": 0.4}, "avg_score": 0.46, "risk_level": "High"}

Example 5
X: Airport with good threat detection, good encryption, good regulatory compliance, adequate incident response plans, adequate user training, adequate access control, and adequate network security.
y: {"scores": {"TDS": 0.6, "DEP": 0.5, "RC": 0.6, "IRP": 0.5, "UT": 0.5, "ACM": 0.5, "NSS": 0.5}, "avg_score": 0.53, "risk_level": "Low"}

NEW INPUT X
Regional airport with a small security operations team that detects only some threats,
encryption on the main databases but not on internal messaging, partial compliance with
ICAO Annex 17 requirements, an incident response plan that exists but has never been tested,
yearly security training for staff, shared administrator accounts on some systems, and
a firewall with basic network segmentation.

Now return the JSON for the NEW INPUT X, in the same format, with explanation and recommendations.
```

## Actual run (few-shot)

```json
{
  "scores": {"TDS": 0.4, "DEP": 0.5, "RC": 0.5, "IRP": 0.4, "UT": 0.6, "ACM": 0.3, "NSS": 0.4},
  "avg_score": 0.44,
  "risk_level": "High",
  "weakest_criteria": ["ACM", "TDS"],
  "explanation": "The regional airport presents a High cybersecurity risk, with an average maturity score of 0.44. Its main weaknesses are access control management and threat detection. Shared administrator accounts reduce accountability and increase the risk of unauthorized access, while the small security operations team detects only some threats. Although encryption protects the main databases, internal messaging remains unencrypted. Regulatory compliance with ICAO Annex 17 is only partial, and the existing incident response plan has never been tested, raising concerns about the airport's ability to respond effectively to cyber incidents. Yearly security training represents a positive security practice, while basic firewall protection and network segmentation provide a moderate level of network security.",
  "recommendations": [
    "Strengthen access control management by eliminating shared administrator accounts, implementing individual privileged accounts, and introducing multi-factor authentication (MFA) and role-based access control (RBAC).",
    "Improve threat detection capabilities by expanding security monitoring, deploying SIEM solutions, increasing SOC coverage, and implementing continuous threat monitoring across critical airport systems.",
    "Improve overall cybersecurity resilience by encrypting internal messaging, addressing ICAO Annex 17 compliance gaps, regularly testing the incident response plan through simulations, and strengthening network segmentation."
  ]
}
```

The average is correct (3.10 / 7 = 0.44) and the 0.5 rule was applied correctly: 0.44 gives `High`.

## Zero-shot vs few-shot (same X, real results)

| Aspect | Prompt 1 (zero-shot) | Prompt 2 (few-shot) |
|---|---|---|
| Examples given | None | 5 (3 High, 2 Low) |
| Scores (TDS, DEP, RC, IRP, UT, ACM, NSS) | 0.45, 0.65, 0.50, 0.40, 0.70, 0.30, 0.60 | 0.4, 0.5, 0.5, 0.4, 0.6, 0.3, 0.4 |
| Average score | 0.51 | 0.44 |
| Predicted class | Low | High |
| Weakest criteria | ACM, IRP | ACM, TDS |
| Explanation | 3 sentences | Longer, covers every criterion |

## Conclusion

- With the same input X, the zero-shot agent gave `Low` (0.51) and the few-shot agent gave `High` (0.44). The input is close to the 0.5 threshold, so small differences in scoring change the class.
- The few-shot scores are lower and more cautious (for example DEP 0.5 instead of 0.65, NSS 0.4 instead of 0.6). This is consistent with the examples, where "partial" or "basic" measures got scores around 0.3-0.5.
- Both runs applied the average and the 0.5 rule correctly. The difference comes from how each criterion was scored, not from the rule.
- Few-shot prompting calibrates the scale, and it gave the class that matches the expected answer for this borderline case. This is one observation, so a larger test set would be needed to measure the effect properly.
