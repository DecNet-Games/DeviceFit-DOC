---
layout: default
title: RouteRunner
nav_order: 2
parent: API Reference
description: "Reference documentation for the RouteRunner MonoBehaviour in Decnet.DeviceFit."
permalink: /docs/api/route-runner/
---

# `RouteRunner` Component

```csharp
namespace Decnet.DeviceFit
public class RouteRunner : UnityEngine.MonoBehaviour
```

The `RouteRunner` executes an automated camera benchmark along checkpoints defined in a `RouteDefinition` asset. It manages warm-up frame discarding, fixture lifecycle hooks, and per-frame telemetry recording.

---

## Fields & Properties

```csharp
public RouteDefinition activeRoute;
```
The serialized route asset containing waypoint positions, rotations, durations, and visual checkpoint tags.

```csharp
public List<RawFrameRecord> capturedFrames;
```
Preallocated list buffer storing recorded frame rows. Preallocated to at least 2048 entries in `StartRoute()` to prevent heap growth during runs.

```csharp
public bool isRunning { get; }
```
Returns `true` while the camera benchmark is actively moving between checkpoints.

---

## Methods

```csharp
public void StartRoute(System.Action<CaptureSession> onComplete = null)
```
Initializes the profiling harness, queries all `FixtureAdapter` instances in the scene, and begins playback along the active route. Invokes `onComplete` with the aggregated `CaptureSession` when finished.

```csharp
public void StopRoute()
```
Halts playback immediately and releases profiler recorders.
