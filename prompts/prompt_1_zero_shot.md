# Prompt 1 – Simple (zero-shot)

One new observation X and no examples. The agent must find the output y from the instructions alone.

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
{
  "scores": {"TDS": 0.0, "DEP": 0.0, "RC": 0.0, "IRP": 0.0, "UT": 0.0, "ACM": 0.0, "NSS": 0.0},
  "avg_score": 0.00,
  "risk_level": "Low or High",
  "weakest_criteria": ["", ""],
  "explanation": "2-3 sentences justifying the result",
  "recommendations": ["3 short, prioritised actions"]
}

INPUT X
Regional airport with a small security operations team that detects only some threats,
encryption on the main databases but not on internal messaging, partial compliance with
ICAO Annex 17 requirements, an incident response plan that exists but has never been tested,
yearly security training for staff, shared administrator accounts on some systems, and
a firewall with basic network segmentation.
```

## Expected answer (reference)

```json
{
  "scores": {"TDS": 0.5, "DEP": 0.4, "RC": 0.5, "IRP": 0.4, "UT": 0.5, "ACM": 0.3, "NSS": 0.6},
  "avg_score": 0.46,
  "risk_level": "High",
  "weakest_criteria": ["ACM", "DEP"],
  "explanation": "The average maturity score is 0.46, below the 0.5 threshold. Shared administrator accounts and partial encryption are the main weaknesses, and the untested incident response plan adds risk.",
  "recommendations": [
    "Replace shared administrator accounts with individual accounts and multi-factor authentication",
    "Extend encryption to internal messaging and other sensitive data",
    "Run a tabletop exercise to test the incident response plan"
  ]
}
```

## Actual run (zero-shot)

```json
{
  "scores": {"TDS": 0.45, "DEP": 0.65, "RC": 0.50, "IRP": 0.40, "UT": 0.70, "ACM": 0.30, "NSS": 0.60},
  "avg_score": 0.51,
  "risk_level": "Low",
  "weakest_criteria": ["ACM", "IRP"],
  "explanation": "The airport demonstrates moderate cybersecurity maturity, with positive measures including annual staff training, database encryption, firewall protection and basic network segmentation. However, shared administrator accounts and an untested incident response plan create significant vulnerabilities. Although the average maturity score is slightly above the 0.5 threshold, improvements are necessary to strengthen overall aviation cybersecurity resilience.",
  "recommendations": [
    "Priority 1: Eliminate shared administrator accounts and implement individual privileged accounts with multi-factor authentication.",
    "Priority 2: Test and regularly update the incident response plan through simulated cybersecurity incidents and recovery exercises.",
    "Priority 3: Improve threat detection capabilities, extend encryption to internal messaging and address remaining ICAO Annex 17 compliance gaps."
  ]
}
```

## Notes

- The average is correct (3.60 / 7 = 0.51) and the 0.5 rule was applied correctly: 0.51 gives `Low`.
- The reference answer above scored the criteria more strictly (0.46, `High`). The two runs differ only in how generously each criterion was scored.
- This observation is close to the threshold, so a small difference in scores changes the class. This is the main weakness of zero-shot: with no examples, the agent has no anchor for what "partial" or "weak" means on the 0-1 scale.
- Prompt 2 (few-shot) adds known (X, y) pairs to calibrate the scores. See `prompt_2_few_shot.md`.
