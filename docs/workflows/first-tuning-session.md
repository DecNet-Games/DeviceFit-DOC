---
layout: default
title: First Tuning Session
nav_order: 1
parent: Workflows Overview
description: "A comprehensive guide to conducting your first performance tuning session on a production scene."
permalink: /docs/workflows/first-tuning-session/
---

# Your First Tuning Session on a Production Scene

Applying a new performance tool to an active game project can be daunting. This workflow outlines the recommended procedure for your initial tuning pass.

```
Step 1: Duplicate Scene / Branch ──> Step 2: Bind Camera Route ──> Step 3: Establish Baseline ──> Step 4: Run Trials ──> Step 5: Journaled Apply
```

---

## 1. Branch Safety & Scene Isolation

Before touching quality settings:
1. Create a dedicated git branch: `git checkout -b perf/devicefit-initial-tuning`.
2. Duplicate your primary gameplay scene as `Scene_PerformanceLab.unity`.
3. Strip any non-deterministic elements (such as random AI spawner scripts) or replace them with fixed `FixtureAdapter` components.

---

## 2. Setting Up the Route Runner

1. In `Scene_PerformanceLab.unity`, create an empty GameObject named `[DeviceFit Runner]`.
2. Attach the `RouteRunner` component.
3. Open **Tools > Decnet > DeviceFit > Create Route**.
4. Fly your Scene view camera through the most demanding section of your level (e.g. looking across the entire map or walking through dense foliage).
5. Add 4 to 6 checkpoints capturing key vistas. Mark at least one checkpoint with dense lighting as a **Visual Checkpoint**.
6. Save the route as `Assets/Routes/Main_Vista_Route.asset` and assign it to the `RouteRunner`.

---

## 3. Capturing the Baseline

1. Open **Tools > Decnet > DeviceFit > Experiment Desk**.
2. Set your execution mode to **In-Editor Emulation**.
3. Create an `ExperimentBudget` targeting your project's framerate (e.g. $16.67\text{ ms}$ for 60 FPS).
4. Click **1. Capture Baseline**.
5. Let the runner complete all 3 passes without moving your mouse or clicking on other applications.

---

## 4. Analyzing & Running Trials

1. Click **2. Review Evidence** to inspect the diagnosed bottleneck.
2. If the bottleneck is GPU-bound, enable **Shadows** and **LOD** in the tuning plan.
3. Click **3. Plan Trials** followed by **4. Run Reviewed Plan**.
4. Review the candidate delta table:
   - Identify candidates that save frame time above the noise floor ($\ge \max(0.5\text{ms}, 2\%)$).
   - Check the **Visual Comparison** window to verify shadow and mesh quality.

---

## 5. Applying and Verifying

1. Select the winning non-dominated candidate.
2. Click **6. Apply Reviewed Config**.
3. Verify that your URP Asset has updated in the Project window.
4. Open **Tools > Decnet > DeviceFit > Applied Changes** to confirm the SHA256 journal entry was created.
5. Re-run **1. Capture Baseline** to confirm that the new steady-state frame time matches the trial prediction!
