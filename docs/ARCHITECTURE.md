# Architecture

## System Layers

The system has 8 layers from raw knowledge to actionable output.

```
┌──────────────────────────────────────┐
│  Layer 1: Knowledge Sources          │
│  Public docs + Real logs + Forums    │
└──────────────────┬───────────────────┘
                   │
                   ▼
┌──────────────────────────────────────┐
│  Layer 2: Cause Library              │
│  Issue patterns, symptoms, signals   │
└──────────────────┬───────────────────┘
                   │
                   ▼
┌──────────────────────────────────────┐
│  Layer 3: Synthetic Log Generator    │
│  Truth engine + Noise engine +       │
│  Calibration engine + Virtual studio │
└──────────────────┬───────────────────┘
                   │
                   ▼
┌──────────────────────────────────────┐
│  Layer 4: Structured Dataset         │
│  10 CSV tables                       │
└──────────────────┬───────────────────┘
                   │
                   ▼
┌──────────────────────────────────────┐
│  Layer 5: Data Analysis + Labels     │
│  EDA, weak supervision, statistics   │
└──────────────────┬───────────────────┘
                   │
                   ▼
┌──────────────────────────────────────┐
│  Layer 6: ML Models                  │
│  Regression, classification,         │
│  anomaly detection, clustering       │
└──────────────────┬───────────────────┘
                   │
                   ▼
┌──────────────────────────────────────┐
│  Layer 7: Risk + Root Cause          │
│  Predicted issue + confidence        │
└──────────────────┬───────────────────┘
                   │
                   ▼
┌──────────────────────────────────────┐
│  Layer 8: Recommendations + Dashboard│
│  Actionable optimization advice      │
└──────────────────────────────────────┘
```

---

## Layer Details

### Layer 1: Knowledge Sources

Three sources feed the system:

**Public knowledge** — official documentation for Nuke, Houdini, Deadline, AYON, OIIO, FFmpeg, render engines. Used to build the cause library, not as direct ML training data.

**Real studio logs** — used locally only, never published. Extracted as anonymous statistics: normal frame time ranges, RAM usage ranges, warning frequency, worker speed variance, failure patterns.

**Controlled simulation rules** — the cause library defines what problems exist, what symptoms they produce, and what features should change in synthetic data.

### Layer 2: Cause Library

Each cause card defines:
- Problem name and category
- Which apps it affects
- Why it happens in real production
- What symptoms appear in logs
- What features change in synthetic data
- What recommendations should be given

See [CAUSE_LIBRARY.md](CAUSE_LIBRARY.md) for all cause cards.

### Layer 3: Synthetic Log Generator

Four sub-engines:

**Truth engine** — controls the real cause of each synthetic job. Hidden from the ML model.

```json
{
  "root_cause": "io_bottleneck",
  "hidden_reason": "large_EXR_output + slow_network_storage",
  "expected_outcome": "low_cpu + high_write_time + slow_job"
}
```

**Noise engine** — adds realism: missing values, wrong labels, log noise, repeated messages, random warnings, machine-specific variance, failed retries, inconsistent naming.

**Calibration engine** — uses real log statistics to tune ranges: normal_frame_time_range, first_frame_penalty_range, peak_ram_distribution, retry_probability, output_size_distribution.

**Virtual studio generator** — simulates projects, sequences, shots, tasks, render nodes, farm pools, Deadline jobs, AYON publish jobs, and delivery processes.

### Layer 4: Structured Dataset

10 CSV tables. See [DATA_MODEL.md](DATA_MODEL.md) for full schemas.

### Layer 5: Data Analysis + Labels

Labeling strategy combines four sources:
1. Synthetic supervised labels (generator knows the truth)
2. Real unlabeled logs (for distribution matching and anomaly detection)
3. Weak supervision rules (e.g., peak RAM > 90% worker RAM = memory_risk)
4. Small human review set (50-200 jobs for uncertain cases)

### Layer 6: ML Models

| Task | Models | Purpose |
|---|---|---|
| Regression | Linear, RF, XGBoost, LightGBM | Predict render time, RAM, output size |
| Binary classification | Logistic, RF, GBM | Predict optimization_needed, will_fail |
| Multi-class classification | RF, GBM, LightGBM | Predict issue category |
| Anomaly detection | Isolation Forest, One-Class SVM, LOF | Detect unusual behavior |
| Clustering | K-Means, DBSCAN, HDBSCAN | Find hidden job groups |

### Layer 7: Risk + Root Cause

The model outputs a risk score and predicted issue category with confidence. Evaluation uses the generator's hidden truth for validation.

Key metric: recall for high-risk jobs is prioritized. Better to warn early than miss a job that wastes farm time.

### Layer 8: Recommendations + Dashboard

The system maps predictions to actionable advice from the cause library.

```
Predicted issue: IO bottleneck
Confidence: 82%

Suggested checks:
1. Check output path
2. Check EXR compression
3. Check storage load
4. Run 5-frame sample
```

Dashboard planned in Streamlit first, then Dash or React + FastAPI.

---

## Data Hierarchy

Real render farms are distributed systems. The data stays hierarchical:

```
Studio
└── Project
    └── Sequence
        └── Shot
            └── Task
                └── Job
                    └── Render/Process Tasks
                        └── Frames
                            └── Events / Logs / Metrics
```

For distributed rendering:

```
1 job → many tasks/chunks → many workers → many frames → many events
```

Important identifiers: `job_id`, `task_id`, `worker_id`, `frame_id`, `event_id`

---

## Pre-Submit vs Post-Render

The system separates two prediction modes to prevent data leakage:

**Pre-submit prediction** — predicts risk before the render starts. Uses only features available at submission time: app, department, frame range, resolution, scene features, worker pool, historical patterns.

**Post-render diagnosis** — analyzes what happened after the render finishes. Uses actual frame time, warnings, errors, peak RAM, CPU usage, retries, output size, event logs.

This separation is critical for honest ML evaluation.

---

## Distributed Render Handling

Key metric for distributed jobs:

```
straggler_ratio = slowest_task_duration / median_task_duration
```

If this is high, one machine or chunk is holding the whole job back. This is a strong feature for worker anomaly and distributed bottleneck detection.
