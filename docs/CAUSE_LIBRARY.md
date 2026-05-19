# Cause Library

The cause library is the brain of the synthetic generator. Each cause card describes a real VFX production problem, why it happens, what symptoms appear in logs, what features change in synthetic data, and what recommendations make sense.

---

## How Cause Cards Work

```
Problem
→ why it happens in real production
→ what symptoms appear in logs
→ what features should change in synthetic data
→ what recommendation should be given
```

The ML model never sees the hidden root cause directly. It only sees observable features and must learn the patterns.

---

## Cause Cards

### 1. memory_risk

**Apps:** Nuke, Houdini, Blender, Arnold, RenderMan, Karma

**Real-life causes:**
- Deep comp data
- Large EXR channels
- High-resolution textures
- Large UDIM count
- Large simulation caches
- Low worker RAM
- Heavy geometry
- High render samples

**Visible features in logs:**
- `high_ram_used_gb`
- `high_ram_percent`
- `retries`
- `failed_or_timeout`
- `slower_frame_time`
- `large_output_size`
- `worker_ram_lower_than_job_need`

**Recommendations:**
- Check deep data and unnecessary channels
- Reduce texture resolution where possible
- Split heavy jobs
- Use high-memory worker pool
- Run 5-frame test render

---

### 2. io_bottleneck

**Apps:** Nuke, Houdini, AYON Delivery

**Real-life causes:**
- Large EXR output
- Slow network storage
- Storage congestion
- Heavy read/write operations
- Non-local source media
- Many small files

**Visible features in logs:**
- `low_cpu_percent`
- `normal_ram_percent`
- `high_write_time_sec`
- `high_output_size_mb`
- `long_frame_end_to_write_gap`
- `network_write_mb_s_spike`
- `disk_write_mb_s_high`

**Recommendations:**
- Check output path
- Test local write speed
- Check network storage load
- Check EXR compression
- Reduce unnecessary channels
- Run small frame range before full submission

---

### 3. worker_anomaly

**Apps:** Deadline, AYON, Nuke, Houdini

**Real-life causes:**
- Slow worker storage
- Bad network connection
- Lower CPU count
- Lower RAM
- Unstable machine
- Wrong pool assignment
- Background process load

**Visible features in logs:**
- One worker much slower than median
- High task duration variance
- Higher retry count on same worker
- Same job type slower on same machine historically
- `straggler_ratio` high

**Recommendations:**
- Check worker health
- Remove worker from pool temporarily
- Compare worker to similar nodes
- Check network and disk performance
- Rerun sample on another worker

---

### 4. first_frame_penalty

**Apps:** Nuke, Houdini, Blender, AYON

**Real-life causes:**
- Scene loading
- Plugin initialization
- Cache warmup
- First asset read
- First output write overhead
- OCIO or plugin startup

**Visible features in logs:**
- First frame time much higher than median
- Stable frame time after first frame
- No major warnings
- No repeated failure

**Recommendations:**
- Run warmup frame
- Compare first frame to median
- Do not classify as full-job issue if later frames are stable

---

### 5. heavy_roto_paint

**Apps:** Nuke

**Department:** Comp

**Real-life causes:**
- Many roto nodes
- Many paint strokes
- Heavy animated masks
- Large image processing area

**Visible features in logs:**
- `high_roto_node_count`
- `high_paint_node_count`
- Stable but slower frames
- Medium to high CPU
- RAM moderate

**Recommendations:**
- Pre-render heavy roto or paint when possible
- Check bounding boxes
- Cache expensive sections
- Run representative test frames

---

### 6. heavy_deep_comp

**Apps:** Nuke

**Department:** Comp

**Real-life causes:**
- Deep EXR input
- Large deep data
- Deep merge operations
- Unnecessary channels

**Visible features in logs:**
- `high_ram_used_gb`
- `high_output_size_mb`
- `slow_write_time_sec`
- `high_io_load`
- Possible memory warnings

**Recommendations:**
- Check deep sample count
- Flatten deep data when safe
- Remove unnecessary channels
- Use high-memory pool

---

### 7. houdini_fx_cache_risk

**Apps:** Houdini

**Department:** FX

**Real-life causes:**
- Large VDB caches
- Large particle count
- High volume resolution
- Heavy simulation cache
- Limited RAM or disk cache

**Visible features in logs:**
- `high_vdb_count`
- `high_particle_count`
- `high_cache_size_gb`
- `frame_time_variance_high`
- `memory_spikes`
- Some frames much slower than others

**Recommendations:**
- Check cache size
- Validate sim range
- Reduce volume resolution for tests
- Run wedge or small frame sample
- Use high-memory or FX pool

---

### 8. color_transcode_risk

**Apps:** AYON, OIIO, FFmpeg, Nuke, Hiero

**Real-life causes:**
- OCIO display/view baked into review frames
- Missing color metadata
- Wrong transfer tag
- Wrong range conversion (full vs limited)
- EXR to PNG to MP4 path
- Mismatch between MOV reference and generated review

**Visible features in logs:**
- `uses_ocio` true
- `display_view_defined` true
- `codec` h264
- `color_primaries_missing_or_inconsistent`
- `color_range_full_vs_limited`
- `representation` review_UHD_h264_png
- `ffmpeg_args_include_colorspace_tags`

**Recommendations:**
- Validate OCIO display and view
- Check FFmpeg color tags
- Compare source MOV and generated MP4 metadata
- Check full vs limited range
- Test PNG review frame interpretation

---

### 9. review_generation_risk

**Apps:** AYON, Hiero, FFmpeg, Deadline

**Real-life causes:**
- Wrong frame padding
- Frame sequence interpreted incorrectly
- Single-frame pattern used as image sequence
- Missing frames in sequence
- Wrong input pattern to FFmpeg
- Output video repeats one frame (frozen video)

**Visible features in logs:**
- `missing_frames_count > 0`
- `file_count` different from expected frame count
- `frame_padding_invalid`
- `ffmpeg_input_pattern_suspicious`
- `generated_video_frame_count` mismatch
- `repeated_frame_detected`

**Recommendations:**
- Validate PNG sequence before FFmpeg
- Check frame padding
- Compare expected and actual frame count
- Inspect FFmpeg input pattern
- Reject review generation before upload if sequence is invalid

---

## Cause Card Structure (YAML Template)

```yaml
issue_category: <category_name>
apps:
  - <app1>
  - <app2>
department: <department>

real_life_reason:
  - <reason 1>
  - <reason 2>

synthetic_signals:
  - <feature change 1>
  - <feature change 2>

hidden_truth:
  root_cause: <cause>
  sub_cause: <sub_cause>

recommendations:
  - <action 1>
  - <action 2>
```

Additional cause cards (nuke_large_bounding_box, lighting_texture_overload, missing_asset, broken_reference, etc.) will be added in Phase 3.
