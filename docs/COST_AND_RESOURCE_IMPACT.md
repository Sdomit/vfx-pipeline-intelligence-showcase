# Cost and Resource Impact

## The Financial Case for Render Intelligence

VFX render farms are expensive infrastructure. Understanding the financial cost of unoptimized jobs is the strongest argument for building a system like this.

---

## What Render Farm Waste Actually Costs

### On-Premise Farm Costs

A typical mid-size VFX studio render farm:

| Resource | Scale | Cost |
|---|---|---|
| Render nodes | 100–500 machines | $8,000–$20,000 per node |
| Power per node | 300–600W continuous | Significant ongoing electricity cost |
| IT and maintenance staff | Dedicated farm support | Ongoing operational cost |
| Storage infrastructure | Petabytes of fast NAS/SAN | High capital and maintenance cost |

If 15–20% of farm jobs run longer than necessary due to detectable and preventable issues — memory pressure, IO bottlenecks, scene complexity, worker anomalies — that represents 15–20% of total farm capacity wasted on avoidable compute time.

On a 200-node farm, that is the equivalent of 30–40 nodes running continuously at cost while producing no additional useful output.

### Cloud Rendering Costs

Studios using cloud burst rendering (AWS, Google Cloud, Azure) face direct per-hour charges:

| Instance type | Approximate cost |
|---|---|
| GPU render instance (high-end) | $3–$12 per hour |
| 20-instance cloud burst job | $60–$240 per hour |
| Poorly configured job running 2x longer | Double the cost, same output |

A single badly submitted cloud job that runs twice as long as necessary because of a preventable scene or settings issue wastes real money immediately. At scale — dozens of such jobs per day — the waste compounds quickly.

---

## What This Tool Saves

### Pre-Submit Prediction (Highest Value)

Catching a risky job **before it runs** is the highest-value intervention. If a job would fail or run 3x longer than necessary:

- 100% of that wasted compute time is recovered
- No rerenders needed
- No farm slots blocked while a failing job retries
- Artist or pipeline team gets an actionable warning instead of a 4-hour failure

### Optimization Recommendations

A job that runs 25% faster due to an optimization suggestion saves that compute time on every worker and every frame:

```
Example:
Job: 100 frames × 10 workers × 8 sec/frame saved per frame = 8,000 seconds
= ~2.2 hours of compute time recovered from a single job
```

Across dozens of jobs per day, optimized throughput is equivalent to adding physical render capacity without purchasing new hardware.

### Worker Anomaly Detection

Identifying one underperforming worker node early prevents that node from dragging down every distributed job it participates in. A straggler node running at 60% of normal speed on a 20-node distributed job extends the entire job's wall-clock time — the other 19 nodes sit idle waiting for it to finish.

### Memory Risk Prevention

A job that fails due to an out-of-memory condition requires a full rerender. Predicting memory risk before submission:

- Prevents the failed render cost (100% wasted compute)
- Prevents the rerender cost
- Prevents the pipeline delay and manual debugging time

### IO Bottleneck Detection

Jobs bottlenecked on storage write speed tie up render nodes while they wait for IO — CPU and GPU idle, nodes occupied, farm slots blocked. Routing jobs away from saturated storage paths or flagging oversized EXR output early recovers those blocked slots.

---

## Resource Efficiency Framing

Beyond direct financial cost, the tool improves **resource efficiency** across the pipeline:

| Resource | How This Tool Helps |
|---|---|
| Compute time (CPU/GPU hours) | Fewer wasted hours from failed or unoptimized renders |
| Power consumption | Less idle-under-load time means lower electricity use |
| Storage throughput | IO bottleneck detection prevents storage congestion |
| Artist and pipeline time | Fewer manual debugging sessions, clearer root causes |
| Production schedule | Faster renders mean less padding required in shot schedules |
| Cloud budget | Pre-submit prediction prevents expensive cloud rerenders |

---

## The Pre-Submit vs Post-Render Cost Difference

| Intervention point | Cost of the problem when caught here |
|---|---|
| Pre-submit (before render starts) | Zero wasted compute — problem prevented entirely |
| During render (early warning) | Partial compute wasted — job can be stopped early |
| Post-render diagnosis | Full compute cost already spent — value is in preventing next time |
| Never detected | Full cost repeated on every similar job |

**Pre-submit prediction has the highest ROI.** This is why the cause library and scene feature analysis (Layer 2 in the long-term vision) are such high-value targets.

---

## What the ML Model Is Optimizing For

In production terms, the ML system is optimizing for:

1. **Recall on high-risk jobs** — better to flag a job that turns out fine than miss a job that wastes the farm
2. **Actionable recommendations** — a warning without a suggested fix has limited value
3. **Pre-submit accuracy** — the earlier the warning, the more compute cost is recovered
4. **Worker routing intelligence** — matching jobs to appropriate pool capacity before submission

These are not just ML metrics. They are direct financial outcomes.

---

## Positioning Statement

> This tool does not just detect problems. It recovers compute time, reduces rerenders, prevents cloud overspend, and gives pipeline and production teams earlier visibility into jobs that would otherwise waste hours of expensive farm capacity before anyone notices.

---

## Note on Claimed Numbers

This document uses illustrative ranges, not claimed benchmarks. The actual savings depend on studio size, farm composition, job mix, and current optimization levels. The project does not claim specific ROI figures because no production deployment exists yet. These figures are provided to frame the value of the problem being solved, not to claim measured outcomes.
