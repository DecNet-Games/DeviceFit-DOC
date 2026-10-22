---
layout: default
title: Budgets & Noise Floor
nav_order: 2
parent: Core Concepts
description: "Why single-frame metrics mislead, how to establish frame-time budgets, and understanding the measurement noise floor."
permalink: /docs/concepts/budgets-and-noise-floor/
---

# Budgets, Repeats, and the Noise Floor

In runtime performance optimization, **noise is ubiquitous**. Background operating system threads, CPU frequency scaling, thermal throttling, and garbage collector invocations create variance between consecutive frames and between sequential benchmark passes.

If your tuning tool does not quantify the noise floor, it will regularly mistake random jitter for genuine optimization wins.

---

## Why Frame Time (ms) Trumps FPS

Framerate (FPS) is non-linear and deeply misleading when evaluating optimization impact:

$$\text{Frame Time (ms)} = \frac{1000}{\text{FPS}}$$

```
At 60 FPS:  16.67 ms per frame
At 30 FPS:  33.33 ms per frame

Case A: Saving 2.0 ms at 60 FPS  (16.67ms -> 14.67ms) boosts framerate from 60 FPS -> 68.2 FPS (+8.2 FPS).
Case B: Saving 2.0 ms at 30 FPS  (33.33ms -> 31.33ms) boosts framerate from 30 FPS -> 31.9 FPS (+1.9 FPS).
```

In both cases, the computational savings is identical (2.0 ms of work removed). FPS skews perception, making high-FPS improvements seem massive and low-FPS improvements seem negligible. **DeviceFit measures all budgets and results in milliseconds (ms) of frame execution time.**

---

## Defining Realistic Budgets

An `ExperimentBudget` specifies three critical metrics:

```csharp
[Serializable]
public class ExperimentBudget
{
    public float targetFrameTimeMs = 16.67f; // 60 FPS target
    public float p95FrameTimeMs = 20.0f;     // 95% of frames must fall below this
    public float p99FrameTimeMs = 25.0f;     // Hard upper ceiling for hitches
    public long maxGcAllocPerFrame = 0;      // Zero GC allocation tolerance on hot loop
}
```

- **Target ($p50$ / Median)**: Represents the steady-state performance during standard camera motion.
- **Tail Latency ($p95$ / $p99$)**: Represents intermittent frame drops caused by asset streaming, particle burst instantiations, or shader compilation.
- **GC Allocation**: Managed heap allocations on the hot path trigger periodic garbage collections. A budget of `0 bytes` ensures no garbage collector spikes.

---

## The Measurement Noise Floor: $\max(0.5\text{ ms}, 2\%)$

In `TrialResult.Evaluate()`, DeviceFit enforces a mathematical noise floor rule:

$$\text{Noise Threshold} = \max\left(0.5\text{ ms},\; 0.02 \times \text{Baseline Frame Time}\right)$$

```
Example 1: Baseline = 20.0 ms
Threshold = max(0.5ms, 0.02 * 20.0ms) = max(0.5ms, 0.4ms) = 0.5 ms
A candidate saving 0.3 ms is classified as: INCONCLUSIVE (Below Noise Floor).

Example 2: Baseline = 40.0 ms
Threshold = max(0.5ms, 0.02 * 40.0ms) = max(0.5ms, 0.8ms) = 0.8 ms
A candidate saving 0.6 ms is classified as: INCONCLUSIVE (Below Noise Floor).
```

### Honest "Nothing Improved" Verdicts

If all candidate trials produce variations smaller than the noise threshold, DeviceFit outputs an honest verdict:

```
[DeviceFit Verdict] NO FEASIBLE CANDIDATE FOUND
All candidate variations fell within the measurement noise floor (+/- 0.4ms).
No material improvement detected without violating quality constraints.
Project settings left untouched.
```

> **Measurement Integrity**  
> We believe an asset that tells you "this change did not make a measurable difference" is infinitely more valuable than one that synthesizes a fake +5% gain to justify its purchase.
