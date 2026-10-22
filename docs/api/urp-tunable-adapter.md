---
layout: default
title: URPTunableAdapter
nav_order: 4
parent: API Reference
description: "Reference documentation for the URPTunableAdapter in Decnet.DeviceFit."
permalink: /docs/api/urp-tunable-adapter/
---

# `URPTunableAdapter` Class

```csharp
namespace Decnet.DeviceFit
public static class URPTunableAdapter
```

The `URPTunableAdapter` queries, mutates, and restores quality properties on Universal Render Pipeline assets without reflection.

---

## Static Methods

```csharp
public static float GetShadowDistance()
public static void SetShadowDistance(float distance)
```
Queries and modifies the active URP asset's maximum directional shadow distance in meters.

```csharp
public static int GetCascadeCount()
public static void SetCascadeCount(int count)
```
Queries and sets shadow cascade count (1, 2, 3, or 4).

```csharp
public static float GetLodBias()
public static void SetLodBias(float bias)
```
Queries and modifies Unity's global `QualitySettings.lodBias`.

```csharp
public static float GetRenderScale()
public static void SetRenderScale(float scale)
```
Queries and modifies the active URP asset's render scale multiplier (e.g. `0.85f`).
