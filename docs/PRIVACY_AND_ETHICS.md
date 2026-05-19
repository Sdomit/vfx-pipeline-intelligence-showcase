# Privacy and Ethics

## Data Handling Policy

This project deals with VFX production data, which is sensitive by nature. Studios cannot share raw logs publicly. This document defines how data is handled.

---

## Core Rules

### No raw studio logs are published

This repository does not contain, and will never contain, raw production logs from any studio. All published data is either fully synthetic or anonymized beyond recognition.

### Anonymize everything

If real logs are used for calibration or statistics, the following must be anonymized or removed:

- Project names
- Shot and sequence names
- Artist or user names
- Machine names and hostnames
- Server paths and mount points
- Client or studio names
- IP addresses
- File paths containing identifiable information
- Any metadata that could identify a production

### No artist ranking or blame

The system detects risky jobs, scene conditions, worker issues, and pipeline bottlenecks. It does not rank, score, or blame individual artists or users.

Artists or users may appear only as anonymized workflow features when needed for pattern analysis (e.g., "user_A tends to submit heavier scenes" becomes a statistical pattern, never a named individual).

**The system says:** "This job has high optimization risk."
**The system never says:** "This artist caused the problem."

### Real logs are used only locally

Real studio logs are used only on local machines for:
- Extracting anonymous statistics (frame time ranges, RAM distributions, warning frequency)
- Calibrating the synthetic generator
- Validating model predictions
- Building baseline distributions

Real logs are never uploaded to public repositories, cloud services, or third-party tools without explicit anonymization.

### Synthetic data is not enough alone

This project is honest about the limitations of synthetic data. Synthetic data teaches the model logic and patterns. Real logs teach the model reality. Neither is sufficient alone.

The project never claims:
- "The model is 100% ready for real production data."
- "Synthetic data makes the model production-ready by itself."

The honest framing:
> We use controlled synthetic VFX pipeline logs to pre-train and evaluate risk prediction models, then validate and calibrate them against anonymized real render and delivery logs.

---

## What Real Logs Are Used For

| Use | Allowed | Notes |
|---|---|---|
| Extract anonymous statistics | Yes | Frame times, RAM ranges, warning counts |
| Calibrate synthetic generator | Yes | Tune ranges and distributions |
| Validate model predictions | Yes | Test on anonymized real data |
| Publish raw logs | No | Never |
| Upload to public tools | No | Never without full anonymization |
| Share with third parties | No | Not without explicit studio approval |
| Train models directly on raw logs | Carefully | Only locally, never published |

---

## What Anonymized Statistics Look Like

Good (safe to share):
```
Normal Nuke comp frame time: 5-12 sec
First frame penalty: 1.5x-3x median
Peak RAM: 20-40 GB
Warnings per job: 0-8
```

Bad (not safe to share):
```
Project "ClientX_Feature" shot sh010 rendered on ws02.studio.local
Artist john.doe submitted job with 35 GB RAM peak
Server path: //nas01/projects/ClientX/shots/sh010/comp/
```

---

## Ethical Principles

1. **Transparency** — the project clearly states what is synthetic, what is planned, and what is implemented
2. **No overclaiming** — no fake benchmarks, no fake accuracy, no fake adoption
3. **No blame** — the system helps teams optimize, it does not judge individuals
4. **Privacy first** — real data stays local and anonymized
5. **Honest research** — synthetic data is a starting point, not the final answer
