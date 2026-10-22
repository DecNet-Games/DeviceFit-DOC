---
layout: default
title: FixtureAdapter
nav_order: 3
parent: API Reference
description: "Reference documentation for the FixtureAdapter base class in Decnet.DeviceFit."
permalink: /docs/api/fixture-adapter/
---

# `FixtureAdapter` Abstract Class

```csharp
namespace Decnet.DeviceFit
public abstract class FixtureAdapter : UnityEngine.MonoBehaviour
```

`FixtureAdapter` is the base lifecycle contract for any dynamic scene entity whose state must be controlled during benchmarks (such as AI spawners, traffic simulators, or interactive combat arenas).

---

## Abstract Methods

```csharp
public abstract void AssertReadiness();
```
Called by `RouteRunner` before a benchmark begins. If the fixture is unready (e.g. required assets are unspawned or navmesh is unbaked), it must throw an `InvalidOperationException` detailing the cause.

```csharp
public abstract void ResetState();
```
Called prior to every repeat benchmark pass. Must reset all internal transforms, pools, and simulation states back to their initial deterministic coordinates.

```csharp
public abstract void OnCheckpointFence(string checkpointId);
```
Invoked when the camera route crosses a named checkpoint fence. Allows the fixture to trigger specific scripted stress events (such as explosion bursts or physics collisions).
