# Synthetic VFX Pipeline Intelligence Lab

> A research and engineering framework for predicting render farm failures, detecting bottlenecks, and recommending optimizations — trained on **real** render logs, with synthetic data used only for safe public demos and testing.

![Status](https://img.shields.io/badge/Status-Active_Development-blue)
![First Product](https://img.shields.io/badge/First_Product-VFX_Render_Risk_Analyzer-orange)
![Focus](https://img.shields.io/badge/Focus-Render_Risk_%7C_Bottlenecks_%7C_ML-green)
![License](https://img.shields.io/badge/License-TBD-lightgrey)

---

> ## Project status — May 2026
>
> Active development. This is an honest, in-progress build, not a finished product.
>
> - ✅ Research, architecture, and cause library — documented
> - 🔧 Real-log parsing + data audit — in progress
> - 🔧 Baseline ML on real render logs — in progress
> - 📋 Synthetic generator (for demos + testing only, **not** for training) — planned
> - 📋 Dashboard + packaging — planned
>
> **The prediction model trains on real render logs, kept private.** Synthetic data is used only for public demos, pipeline testing, and edge cases — never for training or accuracy claims. Any cost or performance figures below are **illustrative examples**, not measured results, until a verified result is published in this repo.

---

## The Problem

VFX studios lose render hours every week to preventable problems:

- Failed renders from memory, IO, or scene issues
- Slow frames that hold up entire jobs
- Worker nodes silently underperforming
- Broken deliveries from wrong color transforms or frame padding
- Pipeline misconfigurations caught only after farm time is wasted

There are almost no public VFX log datasets, because studios cannot share production data. That means few people build ML tools for this domain. This project works on real logs locally and shares only what is safe to share.

---

## The Approach

```
Real render logs (private)
        │
        ▼
   Privacy / anonymization  (Text2AiCleaner engine)
        │
        ▼
   Structured dataset  (jobs / tasks / frames / workers / events)
        │
        ▼
   Data audit + honest labels  (failure / slow / risk)
        │
        ▼
   Feature engineering  (signal measured on real data)
        │
        ▼
   ML models  (regression, classification, anomaly detection)
        │
        ▼
   Risk + root-cause prediction
        │
        ▼
   Optimization recommendations
```

Synthetic data sits **outside** this training path. It is generated separately to demo the tool publicly and to stress-test the pipeline — it never trains the model and never decides which features matter.

---

## First Product: VFX Render Risk Analyzer

The first practical tool. It analyzes logs from common VFX/render tools and produces a risk report with actionable checks.

**Input:** one or more render/delivery log files
**Output:** structured risk analysis with recommendations

```
Job: example_comp_v003
Risk: Medium
Main issue: first-frame penalty + high RAM
Slowest frame: 1021
Median frame time: 7.1 sec
First frame time: 12.8 sec
Peak RAM: 35.8 GB
Warnings: 4

Suggested checks:
1. Run a short test render before the full job
2. Check heavy nodes around the slow frames
3. Check output path and EXR write speed
```

*(Example output for illustration. Real measured results will be published here as they are produced.)*

---

## Why Real Logs, Not Synthetic Training

Synthetic data only contains the relationships its generator was told to include. A model trained on it learns those assumptions, not reality. So:

| Question | Answered by |
| --- | --- |
| What fields exist, what shape, does the pipeline run | Synthetic is fine |
| Which features actually predict failure | **Real data only** (`corr`, mutual information, feature importance) |
| How accurate is the model | **Real held-out data only** |

Domain knowledge proposes what might matter. Real data confirms what does.

---

## Cause Library

The reference of real VFX production problems and their log symptoms — used to guide labeling and recommendations.

| Cause | Apps | Key signal |
| --- | --- | --- |
| `memory_risk` | Nuke, Houdini, Blender, Arnold | High RAM, retries, large output |
| `io_bottleneck` | Nuke, Houdini, AYON | Low CPU + high write time |
| `worker_anomaly` | Deadline, AYON | One worker much slower than median |
| `first_frame_penalty` | Nuke, Houdini, Blender | First frame 1.5–3× slower |
| `heavy_roto_paint` | Nuke | Many roto/paint nodes, slower frames |
| `color_transcode_risk` | AYON, OIIO, FFmpeg | OCIO mismatch, missing color metadata |
| `review_generation_risk` | AYON, FFmpeg, Deadline | Wrong frame padding, frozen video |

---

## ML Tasks (planned, on real data)

| Task | Target | Models |
| --- | --- | --- |
| Regression | render_time, peak_ram, output_size | Linear, Random Forest, XGBoost |
| Binary classification | will_fail, optimization_needed | Logistic, Random Forest, GBM |
| Multi-class | issue_category | Random Forest, LightGBM |
| Anomaly detection | unusual worker/frame/job | Isolation Forest |

---

## Roadmap

| Phase | Description | Status |
| --- | --- | --- |
| 0 | Research + architecture documentation | ✅ Done |
| 1 | Real-log parser + data audit | 🔧 In progress |
| 2 | Real-log analysis notebook | 🔧 In progress |
| 3 | Baseline ML + feature importance (real data) | 📋 Next |
| 4 | Privacy engine integration | 📋 Planned |
| 5 | Synthetic generator (demos + testing) | 📋 Planned |
| 6 | Recommendation layer | 📋 Planned |
| 7 | Dashboard (Streamlit) | 📋 Planned |
| 8 | Portfolio / writeup release | 📋 Planned |

---

## What This Project Is Not

- **Not an artist ranking tool.** It flags risky jobs and conditions, not "bad artists."
- **Not trained on synthetic data.** Synthetic is for demos and testing only.
- **Not production-ready yet.** This is active research and prototyping.
- **Not trained on shared studio data.** Real logs stay local and private.
- **Not a replacement for pipeline TDs.** It supports them with data-driven signals.

---

## Privacy and Ethics

- No raw studio logs are published here
- Project names, shots, users, machines, and paths are anonymized via the Text2AiCleaner engine
- No artist ranking or blame is produced
- Real logs are used only locally
- Synthetic data is clearly separated and never presented as real results

---

## Tech Stack

**Current:** Python, Pandas, NumPy, Scikit-learn, XGBoost, Matplotlib, Jupyter
**Planned:** Streamlit, DuckDB/Polars, LightGBM
**Privacy engine:** [Text2AiCleaner](https://github.com/Sdomit/Text2AiCleaner-public)

---

## Related Repos

- **Privacy engine:** [Text2AiCleaner-public](https://github.com/Sdomit/Text2AiCleaner-public) — anonymizes identifiers into safe, consistent codes that double as ML features.

---

## Author

Built by [Sdomit](https://github.com/Sdomit) — fifteen years in VFX pipeline, render farm operations, and data management, now building data + ML tooling for the production domain.
