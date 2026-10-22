---
layout: default
title: Sample Lab Scenes
nav_order: 6
parent: null
description: "Overview of the 3 ready-made benchmark scenes included with DeviceFit: Courtyard GPU, Interaction CPU, and Texture Wall."
permalink: /docs/sample/lab-scenes/
---

# Sample Lab Scenes

DeviceFit includes three specialized, ready-to-run benchmark scenes located in `Assets/Decnet/DeviceFit/Samples/`.

These scenes allow you to test and verify the entire 11-step tuning loop in under 10 minutes without risking or modifying your production game assets.

```
Sample Scenes:
├── Lab_Courtyard_GPUShadows.unity   <── GPU Shading & Shadow Cascade Stress
├── Lab_Interaction_CPUSpikes.unity  <── CPU Raycast & GC Memory Spikes
└── Lab_Memory_TextureWall.unity     <── VRAM & Platform Texture Importer Downscaling
```

---

## 1. `Lab_Courtyard_GPUShadows.unity`

```
Scene Target: Stress GPU rasterization, high-resolution shadow maps, and distance culling.
Active Route: Courtyard_Tour_Route.asset
Expected Bottleneck: GPU_SHADOWS_AND_RASTER
```

### Scene Architecture
- High-density procedural architecture with intricate pillar geometry.
- Real-time directional sunlight configured with 4 shadow cascades and a 150-meter shadow distance.
- Pre-bound camera route (`Courtyard_Tour_Route`) with 4 waypoints, including visual comparison checkpoints.

### What to Test
1. Open the scene and click **Tools > Decnet > DeviceFit > Experiment Desk**.
2. Run **1. Capture Baseline** to establish reference frame times.
3. Observe the bottleneck diagnosis: `GPU_SHADOWS_AND_RASTER (Confidence: HIGH)`.
4. Run candidate trials to evaluate shadow distance reduction (150m $\to$ 80m) and cascade count reduction (4 $\to$ 2).
5. Compare visual shadow fidelity in the **Captures & Comparisons** window.

---

## 2. `Lab_Interaction_CPUSpikes.unity`

```
Scene Target: Isolate CPU main thread overhead, batching limits, and GC allocation spikes.
Active Component: CpuSpikeFixtureAdapter
Expected Bottleneck: CPU_MAIN_THREAD / MEMORY_GC_PRESSURE
```

### Scene Architecture
- A dynamic interaction arena featuring the `CpuSpikeFixtureAdapter` component.
- The fixture generates controlled CPU workloads:
  - Multi-threaded and single-threaded physics Raycast sweeps against dense obstacle colliders.
  - Periodic GC allocation bursts (simulating unpooled particle or string allocation churn).

### What to Test
1. In the Experiment Desk, observe that frame times spike intermittently above the $p95$ budget.
2. Review the bottleneck verdict: `CPU_MAIN_THREAD` or `MEMORY_GC_PRESSURE`.
3. Test LOD bias adjustments (1.0 $\to$ 0.75) to relieve the CPU render thread.

---

## 3. `Lab_Memory_TextureWall.unity`

```
Scene Target: Verify reversible platform texture downscaling and VRAM reduction.
Procedural Assets: TextureWall_Panel_A.png through Panel_D.png (2048x2048)
Expected Bottleneck: Texture Memory Bandwidth / VRAM Footprint
```

### Scene Architecture
- A gallery of quad surfaces mapped with high-resolution 2048 procedural textures (`TextureWall_Panel_A` through `Panel_D`).
- Designed to test `TextureTrialBuildService.cs` (DF-T05).

### What to Test
1. In the Experiment Definition, enable `allowTextureLimits = true`.
2. Run candidate trials. DeviceFit applies a temporary platform override downscaling the textures to $1024$ and $512$.
3. The asset database re-imports the textures, measures frame times and memory footprint, and then **cleanly restores the original 2048 settings** with zero manual intervention.
