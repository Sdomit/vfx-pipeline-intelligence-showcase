# Roadmap

## Phase-Based Implementation Plan

---

### Phase 0: Research Document

**Status: Done**

- Define project scope and direction
- Define data structure and hierarchy
- Define cause library concept
- Define synthetic-to-real strategy
- Define ML targets and evaluation approach
- Define first prototype scope

**Output:** This documentation set.

---

### Phase 1: Real Log Parser Prototype

**Status: Planned**

**Input:** Deadline/Nuke log files, AYON/Deadline delivery logs

**Output:** `jobs.csv`, `frames.csv`, `workers.csv`, `events.csv`

Focus on extracting:
- Job name and metadata
- Worker name and hardware info
- Frame range and per-frame render times
- CPU/RAM summary
- App, plugin, and version
- Status, warnings, and errors

This phase grounds the project in real data before any synthetic generation.

---

### Phase 2: Real-Log Analysis Notebook

**Status: Planned**

Create a Jupyter notebook that analyzes parsed logs:

- Render time distribution
- First-frame penalty measurement
- Slow frame detection
- Worker comparison
- Memory usage distribution
- Warning/error frequency
- Issue category candidates
- Feature correlation
- Anomaly examples

This phase answers: what does real VFX log data actually look like?

---

### Phase 3: Cause Library v1

**Status: Planned**

Build cause cards for the first set of issues:

- `healthy`
- `memory_risk`
- `io_bottleneck`
- `worker_anomaly`
- `first_frame_penalty`
- `heavy_roto_paint`
- `heavy_deep_comp`
- `houdini_fx_cache_risk`
- `color_transcode_risk`
- `review_generation_risk`

Each card includes: real-life reasons, synthetic signals, hidden truth fields, and recommendations.

---

### Phase 4: Synthetic Data Generator v1

**Status: Planned**

Generate synthetic jobs for Nuke comp, Houdini FX, and AYON delivery.

**Output tables:**
`jobs.csv`, `tasks.csv`, `frames.csv`, `workers.csv`, `events.csv`, `scene_features.csv`, `outputs.csv`, `labels.csv`, `recommendations.csv`

**Initial target:** 10,000 synthetic jobs

Generator includes truth engine (hidden root causes), noise engine (realistic messiness), and calibration engine (ranges from real log statistics).

---

### Phase 5: Baseline ML Models

**Status: Planned**

Train four model types:

1. **Render time regression** — predict frame time and job duration
2. **Optimization-needed classification** — binary yes/no
3. **Issue category classification** — multi-class (memory, IO, worker, etc.)
4. **Anomaly detection** — find unusual jobs without labels

First models: Linear Regression, Logistic Regression, Random Forest, Isolation Forest, K-Means.

Later: XGBoost, LightGBM, HDBSCAN.

---

### Phase 6: Recommendation Layer

**Status: Planned**

Map model output to practical advice from the cause library.

Example:
```
Predicted issue: IO bottleneck
Confidence: 82%

Suggested checks:
1. Check output path
2. Check EXR compression
3. Check storage load
4. Run 5-frame sample
```

---

### Phase 7: Dashboard

**Status: Planned**

Build a dashboard showing:
- Jobs with risk scores
- Issue category breakdown
- Worker performance comparison
- Slowest frames and first-frame penalty
- Straggler ratio for distributed jobs
- Optimization suggestions
- **Estimated compute time saved** per flagged and optimized job
- **Wasted render time recovered** — total hours and equivalent node count
- **Cost impact summary** — illustrative savings based on farm size and job mix

The cost and resource view is a first-class dashboard feature, not an afterthought. Pipeline supervisors and production managers need to see the financial case clearly.

**Stack:** Streamlit first, then Dash or React + FastAPI.

---

### Phase 8: Portfolio/Research Release

**Status: Planned**

Deliverables:
- GitHub repository (this repo)
- README and documentation
- Demo video
- Synthetic dataset sample
- Analysis notebook
- Technical blog post
- LinkedIn post

**Portfolio positioning:**
> A real-log-inspired synthetic VFX production dataset and ML pipeline for predicting render risk, bottlenecks, and optimization needs before and after farm submission.

---

## Build Order Summary

```
Phase 0  →  Research documentation (done)
Phase 1  →  Real log parser prototype
Phase 2  →  Real-log analysis notebook
Phase 3  →  Cause library v1
Phase 4  →  Synthetic data generator v1
Phase 5  →  Baseline ML models
Phase 6  →  Recommendation layer
Phase 7  →  Dashboard
Phase 8  →  Portfolio/research release
```

Each phase builds on the previous one. No phase is skipped.
