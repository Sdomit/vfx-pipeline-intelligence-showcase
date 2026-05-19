# Synthetic VFX Pipeline Intelligence Lab

> A synthetic-data-driven research and engineering framework for studying VFX render farm behavior, production bottlenecks, delivery risks, and pipeline optimization — where real studio data cannot be publicly shared.

![Status](https://img.shields.io/badge/Status-Research_%2B_Prototype_Planning-blue)
![First Product](https://img.shields.io/badge/First_Product-VFX_Render_Risk_Analyzer-orange)
![Focus](https://img.shields.io/badge/Focus-Render_Risk_%7C_Bottlenecks_%7C_Synthetic_Data_%7C_ML-green)
![License](https://img.shields.io/badge/License-TBD-lightgrey)

---

## The Problem

VFX studios lose thousands of render hours every week to preventable problems:

- Failed renders from memory, IO, or scene issues
- Slow frames that hold up entire jobs
- Worker nodes silently underperforming
- Broken deliveries from wrong color transforms or frame padding
- Review videos that freeze or repeat a single frame
- Pipeline misconfigurations that nobody catches until farm time is wasted

There are no public VFX log datasets because studios cannot share production data. That means nobody builds ML tools for this domain. This project changes that.

---

## The Idea

Build a system that generates realistic synthetic VFX pipeline logs, trains ML models on them, and calibrates everything against anonymized real studio logs.

The system predicts and diagnoses:

| Risk Category | Examples |
|---|---|
| Render time | Slow frames, first-frame penalty, job duration spikes |
| Memory | High RAM, texture overload, deep comp data, simulation caches |
| IO | Large EXR output, slow storage, network write bottlenecks |
| Worker | Straggler nodes, hardware mismatch, unstable machines |
| Scene | Heavy roto/paint, large bounding boxes, missing assets |
| Delivery | Wrong color transforms, bad frame padding, frozen review video |
| Pipeline | OCIO misconfiguration, missing metadata, broken paths |

---

## Architecture

```
Real Logs + Public Knowledge
        │
        ▼
   Cause Library
   (real issue patterns)
        │
        ▼
   Synthetic Log Generator
   (truth + noise + calibrated ranges)
        │
        ▼
   Structured Dataset
   (jobs / tasks / frames / workers / events / metrics)
        │
        ▼
   Data Analysis + Weak Labels
        │
        ▼
   Feature Engineering
        │
        ▼
   ML Models
   (regression, classification, anomaly detection, clustering)
        │
        ▼
   Risk + Root Cause Prediction
        │
        ▼
   Optimization Recommendations
        │
        ▼
   Dashboard
```

See [docs/ARCHITECTURE.md](docs/ARCHITECTURE.md) for the full system design.

---

## First Product: VFX Render Risk Analyzer

The first practical tool inside this project. It analyzes logs from Deadline, Nuke, Houdini, AYON, OIIO, FFmpeg, Blender, and render farm workers.

**Input:** one or more render/delivery log files

**Output:** structured risk analysis with actionable recommendations

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
1. Run 5-frame test before full render
2. Check heavy nodes around slow frames
3. Check output path and EXR write speed
```

---

## The Synthetic-to-Real Strategy

Synthetic data alone is not enough. Real studio logs contain things that are hard to simulate: weird artist habits, broken paths, missing plugins, farm instability, silent failures.

The professional approach:

```
Synthetic data  →  teaches the model the logic
Real logs        →  teach the model the reality
Weak supervision →  bridges both
```

The calibration workflow:

```
Real logs → extract anonymous statistics → calibrate generator
→ generate large synthetic dataset → train baseline ML
→ test on hidden scenarios → test on anonymized real logs
→ adjust generator → retrain
```

This project never claims the model is production-ready from synthetic data alone.

---

## Cause Library

The brain of the synthetic generator. Each cause card describes a real VFX production problem, its symptoms in logs, the features it affects, and what should be recommended.

| Cause Card | Apps | Key Signal |
|---|---|---|
| `memory_risk` | Nuke, Houdini, Blender, Arnold | High RAM, retries, large output |
| `io_bottleneck` | Nuke, Houdini, AYON | Low CPU + high write time |
| `worker_anomaly` | Deadline, AYON | One worker much slower than median |
| `first_frame_penalty` | Nuke, Houdini, Blender | First frame 1.5x-3x slower |
| `heavy_roto_paint` | Nuke | Many roto/paint nodes, slower frames |
| `heavy_deep_comp` | Nuke | High RAM, large EXR, slow IO |
| `houdini_fx_cache_risk` | Houdini | Large VDB/particle count, memory spikes |
| `color_transcode_risk` | AYON, OIIO, FFmpeg | OCIO mismatch, missing color metadata |
| `review_generation_risk` | AYON, FFmpeg, Deadline | Wrong frame padding, frozen video |

See [docs/CAUSE_LIBRARY.md](docs/CAUSE_LIBRARY.md) for full cause card definitions.

---

## ML Tasks

| Task | Target | Models |
|---|---|---|
| Regression | render_time, peak_ram, output_size | Linear, Random Forest, XGBoost |
| Binary classification | optimization_needed, will_fail | Logistic, Random Forest, GBM |
| Multi-class classification | issue_category | Random Forest, GBM, LightGBM |
| Anomaly detection | unusual worker/frame/job behavior | Isolation Forest, One-Class SVM |
| Clustering | hidden job groups | K-Means, DBSCAN, HDBSCAN |

---

## Dataset Structure

10 planned CSV tables covering the full render farm data hierarchy.

| Table | Description |
|---|---|
| `jobs.csv` | One row per render, publish, or delivery job |
| `tasks.csv` | One row per farm task or chunk |
| `frames.csv` | One row per rendered or transcoded frame |
| `workers.csv` | One row per render node or workstation |
| `resource_metrics.csv` | CPU, RAM, GPU, disk, network samples |
| `events.csv` | Parsed log events, warnings, errors |
| `scene_features.csv` | Node counts, textures, caches, complexity |
| `outputs.csv` | Generated files, representations, codecs |
| `labels.csv` | Risk level, issue category, root cause |
| `recommendations.csv` | Suggested optimization actions |

See [docs/DATA_MODEL.md](docs/DATA_MODEL.md) for full schemas.

---

## Long-Term Vision

This project is the foundation for a larger idea: an AI-powered intelligence layer that eventually lives inside the render engine itself.

**Three layers of the future system:**

| Layer | Name | Timing | What It Does |
|---|---|---|---|
| 1 | Log-based analysis | **Now — this project** | Parse logs, detect risk patterns, diagnose issues |
| 2 | Pre-submit scene inspector | Near-term | Inspect the actual DCC scene before farm submission via Python APIs |
| 3 | AI inside the render engine | Long-term | Real-time warnings inside Arnold, Karma, RenderMan, Redshift as the artist works |

Layer 3 is where the system warns about material complexity, texture resolution, sample count, UDIM count, and render settings — in real time, before a single frame is submitted.

That AI advisor cannot exist without labeled data. This project builds that data.

See [docs/VISION.md](docs/VISION.md) for the full three-layer breakdown, example outputs, and industry context.

---

## Current Status

| Phase | Description | Status |
|---|---|---|
| Phase 0 | Research documentation | **Done** |
| Phase 1 | Real log parser prototype | Planned |
| Phase 2 | Real-log analysis notebook | Planned |
| Phase 3 | Cause library v1 | Planned |
| Phase 4 | Synthetic data generator v1 | Planned |
| Phase 5 | Baseline ML models | Planned |
| Phase 6 | Recommendation layer | Planned |
| Phase 7 | Dashboard (Streamlit) | Planned |
| Phase 8 | Portfolio/research release | Planned |

See [docs/ROADMAP.md](docs/ROADMAP.md) for details.

---

## What This Project Is Not

- **Not an artist ranking tool.** It detects risky jobs and conditions, not "bad artists."
- **Not a fake AI demo.** No fake benchmarks, no fake screenshots, no fake model accuracy.
- **Not production-ready yet.** This is research and prototype planning.
- **Not trained on public studio data.** No real studio logs are published.
- **Not a replacement for pipeline TDs or render wranglers.** It supports them with data-driven signals.

---

## Why This Project Matters

Most beginner ML projects use generic datasets (house prices, Iris flowers, Titanic survival). This project combines real domain knowledge from VFX production, render farm operations, pipeline engineering, IT infrastructure, and data management.

The intersection:

```
VFX production knowledge
+ render farm behavior
+ pipeline data
+ controlled synthetic simulation
+ machine learning
+ practical optimization advice
```

That makes it personal, useful, and strong for portfolio or research positioning.

---

## Privacy and Ethics

- No raw studio logs are published in this repository
- All project names, shots, users, machines, and paths are anonymized
- No artist ranking or blame is ever produced
- Real logs are used only locally for calibration and statistics
- Synthetic data teaches logic; real logs teach reality

See [docs/PRIVACY_AND_ETHICS.md](docs/PRIVACY_AND_ETHICS.md) for the full policy.

---

## Tech Stack

**Phase 1 (current target):**
Python, Pandas, NumPy, Scikit-learn, Matplotlib, Jupyter Notebook, Streamlit

**Later phases:**
FastAPI, PostgreSQL, DuckDB, Polars, XGBoost, LightGBM, React, MLflow

**Log parsing:**
regex, datetime, pathlib, json, pydantic

---

## Repository Structure

```
vfx-pipeline-intelligence-showcase/
├── README.md
└── docs/
    ├── PROJECT_OVERVIEW.md
    ├── ARCHITECTURE.md
    ├── ROADMAP.md
    ├── DATA_MODEL.md
    ├── CAUSE_LIBRARY.md
    ├── PRIVACY_AND_ETHICS.md
    └── assets/
        └── DIAGRAMS.md
```

The full lab repository with detailed research docs, schemas, and cause cards lives at:
[github.com/Sdomit/Synthetic_VFX_Pipeline_Intelligence_Lab](https://github.com/Sdomit/Synthetic_VFX_Pipeline_Intelligence_Lab)

---

## Documentation

| Document | Description |
|---|---|
| [Project Overview](docs/PROJECT_OVERVIEW.md) | Detailed project description and goals |
| [Architecture](docs/ARCHITECTURE.md) | System layers, data flow, and design |
| [Roadmap](docs/ROADMAP.md) | Phase-based implementation plan |
| [Data Model](docs/DATA_MODEL.md) | All planned CSV tables and schemas |
| [Cause Library](docs/CAUSE_LIBRARY.md) | Issue patterns and cause card definitions |
| [Privacy and Ethics](docs/PRIVACY_AND_ETHICS.md) | Data handling and ethical guidelines |
| [Vision](docs/VISION.md) | Long-term direction — from log analysis to AI inside the render engine |
| [Diagrams](docs/assets/DIAGRAMS.md) | Mermaid architecture and flow diagrams |

---

## Core Principle

> Synthetic data teaches the model the logic.
> Real logs teach the model the reality.
> Weak supervision bridges both.

---

## Author

Built by [Sdomit](https://github.com/Sdomit) — VFX pipeline, data management, and ML research.
