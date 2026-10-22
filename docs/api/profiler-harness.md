---
layout: default
title: ProfilerHarness
nav_order: 1
parent: API Reference
description: "Reference documentation for the ProfilerHarness class in Decnet.DeviceFit."
permalink: /docs/api/profiler-harness/
---

# `ProfilerHarness` Class

```csharp
namespace Decnet.DeviceFit
public class ProfilerHarness : System.IDisposable
```

The `ProfilerHarness` manages low-overhead profiler recorders (`ProfilerRecorder`) and Unity's `FrameTimingManager`. It is designed to sample frame metrics without allocating managed garbage on the hot path.

---

## Properties

```csharp
public bool SupportsGPUTiming { get; }
```
Returns `true` if `FrameTimingManager` GPU timestamps are available and supported by the active platform. In the Unity Editor, this property returns `false`.

```csharp
public bool SupportsFrameTiming { get; }
```
Returns `true` if `FrameTimingManager.IsFeatureEnabled()` is active.

---

## Methods

```csharp
public void Initialize()
```
Starts internal profiler recorders for:
- `"CPU Main Thread Frame Time"` (15 samples buffer)
- `"GC Allocated In Frame"` (15 samples buffer)
- `"Total Used Memory"` (1 sample buffer)

```csharp
public RawFrameRecord SampleCurrentFrame(int frameIndex, string checkpointId, bool isWarmup)
```
Captures the current frame's performance metrics into an unmanaged `RawFrameRecord` value struct. Guaranteed **0 bytes heap allocation** on the calling thread.

```csharp
public void Dispose()
```
Releases all active profiler recorders and cleans up internal buffers.
