---
layout: default
title: Regression Hunting
nav_order: 2
parent: Workflows Overview
description: "How to isolate and diagnose sudden performance drops introduced by recent commits or asset updates."
permalink: /docs/workflows/regression-hunt/
---

# Regression Hunting

A common studio nightmare is the "phantom slowdown": on Friday the game ran smoothly at 60 FPS, but on Monday morning the build stutters at 42 FPS.

DeviceFit provides a structured regression hunt workflow using git bisect and automated route playback.

```
Commit A (60 FPS Baseline) ───────> Git Bisect ───────> Commit B (42 FPS Regression Found)
                                       │
                      Run DeviceFit Batchmode Route
                                       │
                      Compare Metric Dictionaries
                                       │
                      Identify: Added Draw Calls vs Shader Stutter vs New GC Churn
```

---

## 1. Establishing the Regression Delta

1. Checkout your last known good release tag or commit:
   `git checkout v1.2.0`
2. Open the Experiment Desk and load your standard benchmark route.
3. Click **Export Report** and save the baseline report as `Good_Baseline.html`.
4. Return to the regressed branch:
   `git checkout main`
5. Run the same benchmark route and export the report as `Regressed_Current.html`.

---

## 2. Comparing Subsystem Metrics

Open both reports side by side and inspect the individual metric channels:

| Metric Channel | Good Baseline | Regressed Build | Diagnosis |
|:---|:---|:---|:---|
| **CPU Main Thread ($p50$)** | 11.2 ms | 18.9 ms | **CPU Main Thread Regression** (Script logic, pathfinding, or physics). |
| **CPU Render Thread ($p50$)** | 4.1 ms | 12.4 ms | **Batching Breakage** (Material property blocks missing, batch count multiplied). |
| **GPU Frame Time ($p50$)** | 14.8 ms | 23.2 ms | **Rendering Regression** (Shader complexity, overdraw, or shadow cascade expansion). |
| **GC Alloc / Frame** | 0 B | 14.2 KB | **Memory Regression** (Linq usage or new array allocations in `Update()`). |

---

## 3. Automating with Git Bisect

You can combine git bisect with DeviceFit's headless batchmode CI runner:

```powershell
git bisect start
git bisect bad main
git bisect good v1.2.0

# Run DeviceFit headless runner on each step:
git bisect run powershell -Command "Unity.exe -batchmode -nographics -projectPath . -executeMethod Decnet.DeviceFit.Editor.RegressionSuiteService.RunHeadlessRegression -experimentAsset Assets/Settings/CI_Experiment.asset"
```

Git will automatically binary-search through commits, running the benchmark on each commit and identifying the exact pull request that violated the frame time budget.
