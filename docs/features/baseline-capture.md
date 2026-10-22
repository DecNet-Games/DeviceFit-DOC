---
layout: default
title: Baseline Capture
nav_order: 4
parent: Features Overview
description: "How DeviceFit runs automated camera routes, discards warm-up frames, and computes multi-repeat baselines."
permalink: /docs/features/baseline-capture/
---

# Baseline Capture & Warm-Up

A baseline is the reference performance against which all future candidate configurations are measured. If the baseline is flawed, every subsequent optimization decision will be compromised.

```
Baseline Execution Pipeline:
Start Route ──> Discard Warmup (100 frames) ──> Pass 1 Recording ──> Reset State ──> Pass 2 Recording ──> Reset State ──> Pass 3 Recording ──> Sort & Extract Median
```

---

## 1. Warm-Up Frame Rejection

When a scene starts playing, several initialization events distort frame times:
- Shader compilation and PSO creation
- Asset streaming and texture uploading to VRAM
- Garbage collector initialization
- Memory pool expansion

To ensure steady-state accuracy, `RouteRunner` executes a configurable **warm-up phase** (default: 100 frames). All telemetry gathered during warm-up is tagged `isWarmup = true` and completely excluded from baseline statistical calculations.

---

## 2. Multi-Repeat Passes ($\ge 3$ Repeats)

DeviceFit mandates at least three complete repeat passes along the authored route before accepting a baseline:

```
Pass 1: Mean = 21.4ms, Max = 32.1ms (transient disk cache read hitch)
Pass 2: Mean = 21.6ms, Max = 24.2ms
Pass 3: Mean = 21.3ms, Max = 24.0ms

Calculated Median: Mean = 21.4ms, p95 = 23.9ms, Max = 24.2ms (Pass 1 spike discarded)
```

By taking the median across multiple passes, non-reproducible external hitches (such as Windows Update or background antivirus disk scans) are automatically rejected.

---

## 3. Fixture Readiness & State Resets (DF-T02)

Before each repeat pass, `RouteRunner` calls:

```csharp
fixtureAdapter.ResetState();
```

Any custom dynamic entities (such as spawned enemies, particle systems, or physics ragdolls) must be reset to their initial conditions. If a fixture reports that its state cannot be deterministically reset, DeviceFit invalidates the run with an explicit error rather than recording tainted data.
