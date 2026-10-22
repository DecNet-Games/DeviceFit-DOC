---
layout: default
title: Environment Setup
nav_order: 1
parent: Features Overview
description: "How DeviceFit inspects the Unity engine, URP package version, and active graphics API."
permalink: /docs/features/environment-setup/
---

# Environment Setup & Discovery

In `EnvironmentDiscoveryService.cs`, DeviceFit inspects the active runtime context before any benchmark is allowed to run.

```csharp
public static EnvironmentDescriptor InspectEnvironment()
```

---

## Discovered Properties

When you open the **Experiment Desk** or execute a trial, the discovery service inspects:

1. **Unity Engine Version**: Retrieves `Application.unityVersion` (e.g. `6000.5.4f1`).
2. **Render Pipeline Package**: Scans `Packages/manifest.json` for `com.unity.render-pipelines.universal` to extract the exact package version (e.g. `17.5.0`).
3. **Graphics API**: Inspects `SystemInfo.graphicsDeviceType` (Direct3D 11, Direct3D 12, Vulkan, OpenGLES3, or Metal).
4. **Hardware Specifications**: Logs CPU vendor, core count, GPU model name, and system memory.
5. **Profiler Counter Availability**: Determines whether `ProfilerRecorder` and `FrameTimingManager` are active and supported on the current execution target.

---

## Context Invariance: Blocking Invalid Comparisons (DF-T04)

Comparing performance data across different contexts is scientifically invalid:

```
[Incomparable Context Scenarios]
- In-Editor PlayMode  vs  Standalone Development Player
- Direct3D 11 API     vs  Vulkan API
- VSync Enabled       vs  VSync Disabled
- Build ID: Alpha-1   vs  Build ID: Alpha-2
```

In `DF-T04_ContextMismatch_IsMarkedIncomparable`, DeviceFit checks the context fingerprints of the baseline session and candidate session. If any invariant property differs, the trial runner flags the result as **`INCOMPARABLE`** and refuses to calculate a performance delta.

> **Pro Tip**  
> Never compare In-Editor frame times against a Standalone Player. The Unity Editor's GUI loop, Scene view overhead, and Inspector serialization add significant variance that does not exist in player builds.
