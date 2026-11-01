# /prompts/ – AI Agent Prompts

This directory documents how my thesis system is used as an **end-to-end solution run by a single AI agent**.

## The system as one agent

- **Input X:** a free-text description of an aviation organisation's cybersecurity governance (for example an airport audit summary).
- **Output y:** `Low` or `High` cybersecurity risk, plus a short explanation and recommendations.
- **Decision rule:** average maturity score of 7 criteria. `>= 0.5` is **Low**, `< 0.5` is **High**.

The 7 criteria come from Mizrak & Akkartal (2024): TDS (threat detection), DEP (data encryption/protection), RC (regulatory compliance), IRP (incident response plan), UT (user training), ACM (access control management), NSS (network security).

## Pipeline stages handled by the agent

| Stage | What the agent does |
|---|---|
| 1. Data understanding | Reads X and finds evidence for each of the 7 criteria; notes missing information |
| 2. Inference | Gives each criterion a maturity score from 0 to 1 |
| 3. Reasoning | Computes the average and applies the 0.5 threshold; identifies the weakest criteria |
| 4. Output generation | Returns `y`, the scores, a short explanation and prioritised recommendations as JSON |

## Files

| File | Content |
|---|---|
| `prompt_1_zero_shot.md` | One new observation X, no examples |
| `prompt_2_few_shot.md` | Same task plus 5 known (X, y) pairs from Lab 1.2 (`data/examples.csv`) |
| `README.md` | This file |

## How to use

1. Copy the whole prompt from one of the files into an LLM chat (for example Claude).
2. Read the JSON answer and check that `risk_level` matches the average score and the 0.5 rule.
3. Compare the zero-shot and few-shot answers for the same X (see the comparison in `prompt_2_few_shot.md`).
