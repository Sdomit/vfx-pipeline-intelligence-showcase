# Diagrams

Mermaid diagrams for the Synthetic VFX Pipeline Intelligence Lab.

---

## System Architecture

```mermaid
flowchart TD
    A[Public Knowledge + Real Logs] --> B[Cause Library]
    B --> C[Synthetic Log Generator]
    C --> D[Structured Dataset]
    D --> E[Data Analysis]
    E --> F[Labels + Weak Labels]
    F --> G[Feature Engineering]
    G --> H[ML Models]
    H --> I[Risk + Root Cause]
    I --> J[Recommendations]
    J --> K[Dashboard]
```

---

## Synthetic Generator Engines

```mermaid
flowchart LR
    subgraph Generator
        T[Truth Engine] --> S[Synthetic Jobs]
        N[Noise Engine] --> S
        C[Calibration Engine] --> S
        V[Virtual Studio] --> S
    end
    S --> D[Structured Dataset]
```

---

## Data Hierarchy

```mermaid
flowchart TD
    Studio --> Project
    Project --> Sequence
    Sequence --> Shot
    Shot --> Task
    Task --> Job
    Job --> RenderTask[Render/Process Tasks]
    RenderTask --> Frames
    Frames --> Events[Events / Logs / Metrics]
    RenderTask --> Worker
```

---

## Distributed Job Flow

```mermaid
flowchart TD
    J[Job] --> T1[Task/Chunk 1 → Worker A]
    J --> T2[Task/Chunk 2 → Worker B]
    J --> T3[Task/Chunk 3 → Worker C]
    T1 --> F1[Frames 1001-1010]
    T2 --> F2[Frames 1011-1020]
    T3 --> F3[Frames 1021-1030]
```

---

## Labeling Strategy

```mermaid
flowchart TD
    SL[Synthetic Supervised Labels] --> ML[ML Training]
    RL[Real Unlabeled Logs] --> WS[Weak Supervision Rules]
    WS --> WL[Weak Labels]
    WL --> ML
    HR[Human Review 50-200 jobs] --> ML
    ML --> E[Evaluation]
    E --> C[Calibration + Fine-tuning]
```

---

## Pre-Submit vs Post-Render

```mermaid
flowchart LR
    subgraph Pre-Submit
        PS1[App + Department]
        PS2[Frame Range + Resolution]
        PS3[Scene Features]
        PS4[Worker Pool]
        PS5[Historical Patterns]
    end
    subgraph Post-Render
        PR1[Frame Time]
        PR2[Peak RAM + CPU]
        PR3[Warnings + Errors]
        PR4[Output Size]
        PR5[Worker Comparison]
    end
    Pre-Submit --> PM[Prediction Model]
    Post-Render --> DM[Diagnosis Model]
```

---

## Cause Card Flow

```mermaid
flowchart TD
    P[Problem: memory_risk] --> W[Why: deep comp + large textures + low RAM]
    W --> S[Symptoms: high RAM, retries, slow frames]
    S --> F[Features: ram_used_gb, retry_count, frame_time]
    F --> R[Recommendation: check deep data, split job, use high-mem pool]
```

---

## Implementation Roadmap

```mermaid
gantt
    title Project Phases
    dateFormat  YYYY-MM-DD
    section Research
    Phase 0 - Research Doc     :done, p0, 2025-01-01, 30d
    section Prototype
    Phase 1 - Log Parser       :p1, after p0, 30d
    Phase 2 - Analysis Notebook :p2, after p1, 20d
    Phase 3 - Cause Library v1  :p3, after p2, 15d
    section Generation
    Phase 4 - Synthetic Generator :p4, after p3, 30d
    section ML
    Phase 5 - Baseline Models   :p5, after p4, 25d
    Phase 6 - Recommendations   :p6, after p5, 15d
    section Product
    Phase 7 - Dashboard         :p7, after p6, 20d
    Phase 8 - Portfolio Release  :p8, after p7, 15d
```
