---
layout: default
title: Budget Profiles
nav_order: 2
parent: Features Overview
description: "Configuring target frame times, tail latency bounds, and memory limits across device tiers."
permalink: /docs/features/budget-profiles/
---

# Budget Profiles

A performance target cannot be achieved without objective boundaries. DeviceFit encapsulates performance criteria into `ExperimentBudget` assets.

---

## The Metric Model

A complete budget establishes boundaries across multiple statistical dimensions:

```
Target Distribution:
┌───────────────────────┬──────────────────────┬──────────────────────┐
│ p50 (Median)          │ p95 (Tail Latency)   │ p99 (Hard Limit)     │
│ Steady-state target   │ Minor frame hitches  │ Maximum stutter      │
│ e.g. <= 16.67ms       │ e.g. <= 20.0ms       │ e.g. <= 25.0ms       │
└───────────────────────┴──────────────────────┴──────────────────────┘
```

### 1. Steady-State Median ($p50$)
The midpoint of all recorded frame times along the camera route. If your goal is 60 FPS, your $p50$ budget must be $\le 16.67\text{ ms}$.

### 2. Tail Latency ($p95$)
95% of frames must execute faster than this threshold. This catches frequent stuttering during camera pans or rapid entity spawns.

### 3. Maximum Spike ($p99$)
The 99th percentile represents extreme outliers. It isolates asset loading hitches or heavy physics collision steps.

### 4. Managed GC Allocation Limit
The maximum allowed garbage collector allocation per frame on the hot loop (typically `0 bytes`).

---

## Device Profile Presets

DeviceFit allows you to define reusable tier profiles (`VerifiedDeviceProfile.cs`):

| Profile Name | Target API | Target $p50$ | Target $p95$ | Max GC / Frame |
|:---|:---|:---|:---|:---|
| **High Tier Desktop** | D3D11 / D3D12 | 8.33 ms (120 FPS) | 11.0 ms | 0 B |
| **Mid Tier Desktop / Laptop** | D3D11 | 16.67 ms (60 FPS) | 20.0 ms | 0 B |
| **Mainstream Mobile (Target)** | Vulkan | 33.33 ms (30 FPS) | 40.0 ms | 0 B |

> **Validation Rule (DF-T09)**  
> If an experiment fails to meet its budget, DeviceFit reports an explicit **Budget Failure**. It retains the measured candidate data for analysis, but will never falsely mark an over-budget candidate as a "Success".
