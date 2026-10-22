---
layout: default
title: Bottleneck Analysis
nav_order: 5
parent: Features Overview
description: "How DeviceFit classifies the dominant bottleneck between CPU main thread, render thread, GPU, or GC memory."
permalink: /docs/features/bottleneck-analysis/
---

# Bottleneck Analysis

Before attempting to optimize quality settings, you must know **which hardware stage is limiting your framerate**.

Optimizing shadow resolution or texture sizes will produce zero framerate gain if your game is CPU-bound on complex pathfinding or physics. Conversely, stripping mesh colliders will not help a scene choked by fill-rate and post-processing.

```
                  ┌──────────────────────────────┐
                  │ Is GC Alloc > 0 KB / Frame?  │
                  └──────────────┬───────────────┘
                                 │
                 YES ────────────┴──────────── NO
                  │                            │
                  ▼                            ▼
       [MEMORY_GC_PRESSURE]       ┌─────────────────────────┐
                                  │ Compare CPU vs GPU Time │
                                  └────────────┬────────────┘
                                               │
                       ┌───────────────────────┴───────────────────────┐
                       ▼                                               ▼
          GPU Time > CPU Time + 1.0ms                    CPU Time > GPU Time + 1.0ms
                       │                                               │
                       ▼                                               ▼
           [GPU_SHADOWS_AND_RASTER]                      Compare Main Thread vs Render
                                                                       │
                                                       ┌───────────────┴───────────────┐
                                                       ▼                               ▼
                                              Main Thread High                Render Thread High
                                                       │                               │
                                                       ▼                               ▼
                                              [CPU_MAIN_THREAD]               [CPU_RENDER_THREAD]
```

---

## Bottleneck Classifications

`BottleneckAnalyzerService.cs` analyzes the median baseline data and outputs a verdict with confidence ratings:

### 1. `GPU_SHADOWS_AND_RASTER`
- **Condition**: Measured GPU frame time consistently exceeds CPU main thread time by $> 1.0\text{ ms}$.
- **Candidate Solutions**: Reduce shadow distance, lower shadow cascade counts, decrease render scale, disable SSAO or depth-of-field.

### 2. `CPU_MAIN_THREAD`
- **Condition**: CPU main thread time exceeds GPU time, with low render thread wait.
- **Candidate Solutions**: Adjust LOD bias to decrease mesh complexity, reduce particle simulation counts, optimize scripts.

### 3. `CPU_RENDER_THREAD`
- **Condition**: Render thread time dominates, indicating high draw call submission overhead.
- **Candidate Solutions**: Increase GPU instancing, enable SRP Batcher, adjust LOD distances to reduce batch counts.

### 4. `MEMORY_GC_PRESSURE`
- **Condition**: Profiler records non-zero GC allocations on hot frames, triggering periodic GC hitches.
- **Candidate Solutions**: Audit zero-allocation buffers, convert classes to structs, eliminate string concatenations in update loops.

---

## Confidence Limits

The analyzer reports an explicit confidence percentage based on the separation of timings across all valid frames. If CPU and GPU timings are within $0.5\text{ ms}$ of each other, DeviceFit reports:
`Bottleneck: BALANCED / CO-LIMITED (Confidence: LOW)`.
This warns you that improving one subsystem will immediately hit the other bottleneck ceiling.
