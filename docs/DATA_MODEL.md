# Data Model

## Planned Dataset Tables

The dataset follows the natural hierarchy of a VFX render farm:

```
Job → Tasks/Chunks → Frames → Events/Metrics
         ↓
       Workers
```

All tables are planned. None are generated yet.

---

## 1. jobs.csv

One row per high-level render, publish, or delivery job.

**Core fields:**

| Field | Type | Description |
|---|---|---|
| `job_id` | string | Unique job identifier |
| `project` | string | Anonymized project name |
| `sequence` | string | Sequence name |
| `shot` | string | Shot name |
| `department` | string | comp, fx, lighting, delivery |
| `task_name` | string | Task within shot |
| `app` | string | Nuke, Houdini, Blender, AYON |
| `plugin` | string | Deadline plugin used |
| `submit_user` | string | Anonymized user ID |
| `submit_time` | datetime | When the job was submitted |
| `start_time` | datetime | When the job started rendering |
| `end_time` | datetime | When the job finished |
| `total_duration_sec` | float | Total wall-clock time |
| `status` | string | completed, failed, timeout |
| `priority` | int | Farm priority |
| `frame_start` | int | First frame number |
| `frame_end` | int | Last frame number |
| `total_frames` | int | Number of frames |
| `output_type` | string | EXR, PNG, MP4 |
| `job_type` | string | render, publish, delivery, review |

**Derived fields:**

| Field | Type | Description |
|---|---|---|
| `avg_frame_time_sec` | float | Average time per frame |
| `max_frame_time_sec` | float | Slowest frame |
| `p95_frame_time_sec` | float | 95th percentile frame time |
| `frame_time_std_sec` | float | Standard deviation |
| `total_output_size_gb` | float | Total output file size |
| `warning_count` | int | Total warnings |
| `error_count` | int | Total errors |
| `retry_count` | int | Total retries |
| `optimization_needed` | bool | Label: needs optimization |
| `risk_level` | string | low, medium, high, critical |

---

## 2. tasks.csv

One row per farm task or chunk (part of a distributed job).

| Field | Type | Description |
|---|---|---|
| `task_id` | string | Unique task identifier |
| `job_id` | string | Parent job ID |
| `worker_id` | string | Assigned worker |
| `chunk_start_frame` | int | First frame in chunk |
| `chunk_end_frame` | int | Last frame in chunk |
| `chunk_frame_count` | int | Frames in this chunk |
| `start_time` | datetime | Task start |
| `end_time` | datetime | Task end |
| `duration_sec` | float | Task duration |
| `status` | string | completed, failed, requeued |
| `retry_count` | int | Number of retries |
| `error_type` | string | Error category if failed |

---

## 3. frames.csv

One row per rendered or transcoded frame.

**Core fields:**

| Field | Type | Description |
|---|---|---|
| `frame_id` | string | Unique frame identifier |
| `job_id` | string | Parent job ID |
| `task_id` | string | Parent task ID |
| `frame_number` | int | Frame number |
| `frame_index` | int | Position in sequence (0-based) |
| `worker_id` | string | Worker that rendered this frame |
| `render_time_sec` | float | Time to render |
| `write_time_sec` | float | Time to write output |
| `output_file` | string | Output file path (anonymized) |
| `output_size_mb` | float | File size |
| `status` | string | completed, failed |
| `warning_count` | int | Warnings during this frame |
| `error_count` | int | Errors during this frame |

**Derived fields:**

| Field | Type | Description |
|---|---|---|
| `is_first_frame` | bool | First frame in job |
| `is_last_frame` | bool | Last frame in job |
| `is_slow_frame` | bool | frame_time > median * 1.25 |
| `is_spike` | bool | Outlier frame time |
| `relative_frame_time` | float | Ratio to median |
| `frame_time_zscore` | float | Z-score vs job median |

---

## 4. workers.csv

One row per render node or workstation.

| Field | Type | Description |
|---|---|---|
| `worker_id` | string | Unique worker identifier |
| `worker_name` | string | Machine name (anonymized) |
| `os` | string | Operating system |
| `cpu_count` | int | CPU core count |
| `total_ram_gb` | float | Total RAM |
| `gpu_name` | string | GPU model |
| `gpu_vram_gb` | float | GPU VRAM |
| `storage_type` | string | SSD, HDD, NVMe, network |
| `network_speed_gbps` | float | Network speed |
| `site` | string | Location/site |
| `pool` | string | Farm pool name |
| `active` | bool | Currently active |

**Derived fields:**

| Field | Type | Description |
|---|---|---|
| `worker_performance_score` | float | Historical speed metric |
| `worker_failure_rate` | float | Failure frequency |
| `avg_job_duration_multiplier` | float | Speed vs farm median |
| `io_score` | float | Storage performance metric |

---

## 5. resource_metrics.csv

One row per sampled time point or per-job summary.

| Field | Type | Description |
|---|---|---|
| `metric_id` | string | Unique metric ID |
| `job_id` | string | Parent job |
| `task_id` | string | Parent task |
| `worker_id` | string | Worker |
| `timestamp` | datetime | Sample time |
| `cpu_percent` | float | CPU usage |
| `ram_used_gb` | float | RAM used |
| `ram_percent` | float | RAM percentage |
| `gpu_percent` | float | GPU usage |
| `gpu_vram_used_gb` | float | GPU VRAM used |
| `disk_free_gb` | float | Free disk space |
| `disk_read_mb_s` | float | Disk read speed |
| `disk_write_mb_s` | float | Disk write speed |
| `network_read_mb_s` | float | Network read speed |
| `network_write_mb_s` | float | Network write speed |

---

## 6. events.csv

One row per parsed log event.

| Field | Type | Description |
|---|---|---|
| `event_id` | string | Unique event ID |
| `job_id` | string | Parent job |
| `task_id` | string | Parent task |
| `worker_id` | string | Worker |
| `timestamp` | datetime | Event time |
| `severity` | string | info, warning, error, critical |
| `source` | string | Deadline, Nuke, AYON, FFmpeg |
| `event_type` | string | See list below |
| `message` | string | Raw message |
| `normalized_message` | string | Cleaned/normalized message |
| `event_code` | string | Standardized code |

**Event types:** `plugin_initialize`, `start_job`, `render_task`, `frame_start`, `frame_end`, `write_output`, `warning`, `error`, `process_exit`, `cleanup`, `transcode_start`, `transcode_end`, `api_request`, `publish_validation`

---

## 7. scene_features.csv

One row per scene, workfile, or version.

**Nuke/comp fields:**

| Field | Type | Description |
|---|---|---|
| `scene_id` | string | Unique scene ID |
| `job_id` | string | Parent job |
| `app` | string | Application |
| `department` | string | Department |
| `version` | int | Version number |
| `node_count` | int | Total nodes |
| `read_node_count` | int | Read nodes |
| `write_node_count` | int | Write nodes |
| `merge_node_count` | int | Merge nodes |
| `roto_node_count` | int | Roto nodes |
| `paint_node_count` | int | Paint nodes |
| `deep_nodes` | int | Deep processing nodes |
| `blur_nodes` | int | Blur/defocus nodes |
| `transform_nodes` | int | Transform nodes |
| `colorspace_nodes` | int | Colorspace nodes |
| `plugin_count` | int | External plugins |
| `external_asset_count` | int | External file references |
| `missing_asset_count` | int | Missing references |

**Houdini/FX fields:**

| Field | Type | Description |
|---|---|---|
| `geo_count` | int | Geometry objects |
| `particle_count` | int | Particle count |
| `vdb_count` | int | VDB volumes |
| `sim_cache_size_gb` | float | Simulation cache size |
| `solver_type` | string | Solver used |
| `volume_resolution` | int | Volume resolution |
| `packed_prim_count` | int | Packed primitives |

**Lighting/rendering fields:**

| Field | Type | Description |
|---|---|---|
| `samples` | int | Render samples |
| `motion_blur` | bool | Motion blur enabled |
| `depth_of_field` | bool | DOF enabled |
| `ray_depth` | int | Ray bounce depth |
| `light_count` | int | Number of lights |
| `texture_resolution_max` | int | Largest texture |
| `udim_count` | int | UDIM tile count |

---

## 8. outputs.csv

One row per generated file or representation.

| Field | Type | Description |
|---|---|---|
| `output_id` | string | Unique output ID |
| `job_id` | string | Parent job |
| `representation` | string | exr, review_UHD_h264_png |
| `ext` | string | File extension |
| `codec` | string | Codec used (h264, prores) |
| `colorspace` | string | Output colorspace |
| `display` | string | OCIO display |
| `view` | string | OCIO view |
| `resolution_width` | int | Width |
| `resolution_height` | int | Height |
| `frame_padding` | int | Frame number padding |
| `file_count` | int | Number of files |
| `total_size_gb` | float | Total output size |
| `avg_file_size_mb` | float | Average file size |
| `output_path_valid` | bool | Path exists and writable |
| `missing_frames_count` | int | Missing frames in sequence |

---

## 9. labels.csv

One row per job or task label.

| Field | Type | Description |
|---|---|---|
| `label_id` | string | Unique label ID |
| `job_id` | string | Parent job |
| `optimization_needed` | bool | Needs optimization |
| `risk_level` | string | low, medium, high, critical |
| `issue_category` | string | See categories below |
| `root_cause` | string | Detailed cause |
| `confidence` | float | Label confidence (0-1) |
| `source` | string | synthetic, weak_label, human |

**Issue categories:** `healthy`, `slow_render`, `memory_risk`, `cpu_bound`, `gpu_bound`, `io_bottleneck`, `network_bottleneck`, `worker_anomaly`, `plugin_overhead`, `scene_complexity`, `heavy_roto`, `heavy_deep_comp`, `large_texture_memory`, `large_sim_cache`, `missing_asset`, `broken_reference`, `bad_frame_padding`, `color_transcode_risk`, `review_generation_risk`, `deadline_risk`

---

## 10. recommendations.csv

One row per suggested action.

| Field | Type | Description |
|---|---|---|
| `recommendation_id` | string | Unique recommendation ID |
| `job_id` | string | Parent job |
| `issue_category` | string | Related issue |
| `recommendation_text` | string | Actionable suggestion |
| `expected_impact` | string | high, medium, low |
| `priority` | int | Suggestion priority |

**Example recommendations:**
- Run 5-frame test render before full submission
- Check bounding boxes and unnecessary channels
- Reduce preview samples
- Check 8K or 16K textures
- Validate missing paths before farm submission
- Check OCIO display/view profile
- Check worker pool assignment
- Validate frame padding before FFmpeg
