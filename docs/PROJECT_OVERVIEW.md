# Project Overview

## Synthetic VFX Pipeline Intelligence Lab

**Status:** Research + Prototype Planning
**Version:** 2.0
**First Product:** VFX Render Risk Analyzer

---

## What is this project?

A research and engineering framework for building a VFX pipeline intelligence system. The system uses controlled synthetic logs, real-log calibration, weak supervision, and machine learning to predict render and delivery risks.

The core idea: generate realistic VFX production logs inspired by real Deadline, Nuke, Houdini, AYON, OIIO, FFmpeg, and render farm behavior. Use those logs to train ML models that predict problems before or after farm submission.

---

## What does it predict?

The system targets these risk categories:

- **Render time** — will this job be slow?
- **Failure risk** — will this job fail or timeout?
- **Memory risk** — will this job run out of RAM?
- **IO bottlenecks** — is storage or network the limiting factor?
- **CPU/GPU bottlenecks** — is compute the limiting factor?
- **Worker problems** — is a specific machine underperforming?
- **Scene optimization** — does the scene need cleanup before submission?
- **Delivery risk** — will the transcode, color, or review generation break?
- **Pipeline configuration** — is something misconfigured in the pipeline?
- **Frame sequence issues** — wrong padding, missing frames, frozen video?

---

## Professional framing

This project is not about ranking artists or assigning blame.

It detects risky jobs, scene conditions, worker issues, and pipeline bottlenecks.

**The system says:**
- "This job has high optimization risk."
- "This render may need checking."
- "The likely issue is memory, IO, worker anomaly, or review generation risk."

**The system never says:**
- "This artist is bad."
- "This user caused the problem."

Artists or users exist only as anonymized workflow features when needed — never as a blame target.

---

## Why this is a strong niche

Most ML projects use generic datasets. This project combines real domain knowledge across:

- VFX production workflows
- Render farm operations
- Pipeline engineering (AYON, Deadline, Nuke, Houdini)
- IT infrastructure and data management
- Synthetic data generation
- Machine learning
- Practical optimization recommendations

It solves a real problem: wasted farm time, failed renders, slow reviews, broken deliveries, worker bottlenecks, bad frame sequences, wrong color transforms, and manual debugging pressure.

---

## Honest value assessment

| Angle | Rating | Honest take |
|---|---:|---|
| Portfolio value | 8.5/10 | Very strong — connects VFX, pipeline, logs, IT, and ML |
| Research value | 7/10 | Strong if synthetic data is validated against real logs |
| Business value now | 6/10 | Studios may pay for a tool that saves farm time |
| Difficulty | 8/10 | Possible, but scope control is critical |
| Best first version | Doable | Parser, analysis, risk labels, and dashboard from real logs |

---

## Financial and Resource Value

VFX render farms are expensive. The financial case for this tool is direct:

- A failed job that rerenders pays 100% of its compute cost twice
- A job running 2× longer than necessary wastes double the farm slot time and cloud budget
- A straggler worker on a 20-node distributed job holds all other nodes idle
- IO-bottlenecked nodes occupy farm slots while CPU and GPU sit idle
- Cloud burst jobs misconfigured at submission run up per-hour charges with no added output

**Pre-submit prediction has the highest ROI.** Catching a risky job before it runs recovers 100% of the wasted compute. Post-render diagnosis still has value — it prevents the same waste on the next similar job — but pre-submit is where the largest savings live.

At scale across a production pipeline, recovering even 10–15% of wasted farm time is equivalent to adding physical render nodes without purchasing new hardware.

See [COST_AND_RESOURCE_IMPACT.md](COST_AND_RESOURCE_IMPACT.md) for the full financial breakdown.

---

## Core research questions

1. Can we predict if a render will be slow before it starts?
2. Can we detect if a job is memory-bound, CPU-bound, GPU-bound, IO-bound, or pipeline-bound?
3. Can we identify scene, worker, or workflow patterns that cause wasted render time?
4. Can synthetic logs teach a model useful behavior before enough real studio data is available?
5. Can real logs be used safely to calibrate the generator without exposing studio data?
6. Can weak supervision bridge the gap between synthetic labels and messy real logs?
7. Can the system generate useful optimization suggestions instead of only predictions?

---

## The synthetic-to-real principle

Synthetic data can teach the model useful logic, but it cannot make the model 100% ready for real production data by itself.

Real studio logs include things that are hard to simulate: weird habits, broken paths, missing plugins, farm instability, storage congestion, wrong OCIO setup, old scripts, random Deadline behavior, silent failures, inconsistent logs, missing metadata, temporary network issues.

The professional goal:

```
Synthetic data  →  strong pre-trained model
Real logs        →  calibration and validation
Real use         →  continuous improvement
```

> Synthetic data teaches the model the logic. Real logs teach the model the reality.

---

## First product: VFX Render Risk Analyzer

**Input:** Deadline/Nuke-style log files, AYON/Deadline delivery logs

**Output:** structured CSV with risk analysis and recommendations

First analysis targets:
- `is_slow_frame`
- `first_frame_penalty`
- `worker_anomaly`
- `warning_count`
- `peak_ram_risk`

The prototype focuses on Nuke comp renders and AYON delivery jobs first, then expands to Houdini, Blender, and other DCC tools.

---

## Build order

```
1. Real log parser
2. Real-log analysis
3. Cause library
4. Synthetic generator
5. Baseline ML
6. Recommendation layer
7. Dashboard
```

This order ensures the project is grounded in real data before generating synthetic data or training models.
