---
layout: default
title: Capture Sessions
nav_order: 3
parent: Features Overview
description: "Zero-allocation per-frame metrics collection, RawFrameRecord struct, and multi-repeat statistical aggregation."
permalink: /docs/features/capture-sessions/
---

# Capture Sessions & Data Models

During benchmark execution, DeviceFit collects telemetry for every individual frame rendered. Because allocating objects inside the per-frame sampling loop would distort the benchmark itself, the capture architecture is engineered for **strictly zero managed heap allocation**.

---

## The `RawFrameRecord` Value Struct

In `Assets/Decnet/DeviceFit/Runtime/Contracts/CaptureSession.cs`, each frame's metrics are stored in an unmanaged value struct:

```csharp
[Serializable]
public struct RawFrameRecord
{
    public int frameIndex;
    public float timestampSeconds;
    public string checkpointId; // Null or interned string
    public bool isWarmup;

    // Millisecond timings (Nullable floats indicate availability)
    public float? cpuFrameTimeMs;
    public float? cpuMainThreadMs;
    public float? cpuRenderThreadMs;
    public float? gpuTimeMs;

    // Memory & allocations
    public long? gcAllocBytes;
    public float? totalAllocatedMemoryMB;
}
```

### Why a `readonly struct`?
In earlier development versions, `RawFrameRecord` was declared as a C# `class`. Allocating a new class instance every frame generated ~163 to 737 bytes of garbage per sample directly on the managed heap. This triggered periodic garbage collection pauses during route playback, distorting the very frame times being measured.

Converting `RawFrameRecord` to a `struct` ensures that calling `ProfilerHarness.SampleCurrentFrame()` allocates **strictly 0 bytes** on the managed heap.

---

## Statistical Aggregation: `MetricSeries`

Raw frame rows are converted into statistical distributions via `MetricSeries.cs`:

```csharp
[Serializable]
public class MetricSeries
{
    public string metricName;
    public int sampleCount;
    public float min;
    public float max;
    public float mean;
    public float standardDeviation;
    public float p50; // Median
    public float p95;
    public float p99;
}
```

### Multi-Repeat Median Calculation
When a benchmark route executes over 3 passes, DeviceFit calculates the per-frame median:
1. Warm-up frames (default: first 100 frames) are discarded.
2. For each checkpoint and timeline frame index, values across the 3 repeats are sorted.
3. The true median is extracted, eliminating temporary spikes caused by OS background processes.
