---
layout: default
title: Limitations & Honesty
nav_order: 8
parent: null
description: "Comprehensive review of platform boundaries, out-of-scope targets, and the platform NOT VERIFIED state."
permalink: /docs/limitations/
---

# Limitations & Platform Honesty Fence

At Decnet Games, we hold our documentation to the highest standard of technical honesty. This page details what DeviceFit does **not** do and clarifies our current verification boundaries.

---

## 1. Platform Behavior Is NOT VERIFIED

> **MANDATORY NOTICE**  
> In the current 1.0 release cycle, **NO standalone Windows player (D3D11) binary runs and NO physical Android device (Vulkan) runs have taken place on real hardware**.
> 
> While target descriptors, budget data models, and TCP socket pairing protocols are implemented in the code, all platform-dependent behavior is labeled **NOT VERIFIED**. DeviceFit makes **zero claims** of device-measured framerates or hardware power metrics.

Do not purchase DeviceFit assuming it includes pre-packaged hardware telemetry databases for hundreds of consumer phones. It provides the **tooling** for you to measure your own game on your own paired hardware.

---

## 2. Completely Out of Scope

The following platforms and engines are explicitly **unsupported**:

- **iOS and Apple Metal**: Unsupported. No iOS socket transport or Metal profiling hooks are provided.
- **Game Consoles (PlayStation, Xbox, Nintendo Switch)**: Completely out of scope. Proprietary console profiling APIs are not implemented.
- **Built-in Render Pipeline (BiRP)**: Unsupported. DeviceFit's tunable adapters require URP.
- **High Definition Render Pipeline (HDRP)**: Unsupported.
- **Unity Versions Prior to Unity 6**: Unsupported. DeviceFit relies on modern C# 9 features and Unity 6 profiling APIs.

---

## 3. No Automatic "Optimization Magic"

- **DeviceFit is not a compiler or code re-writer**: It will not automatically optimize your C# scripts, fix memory leaks, or simplify 3D meshes.
- **DeviceFit never promises higher framerates**: If your game is bottlenecked by unoptimized CPU simulation or poorly designed shaders, tuning quality settings may produce negligible gains. When this happens, DeviceFit outputs an honest "No Feasible Candidate" report rather than inventing fake improvements.

---

## 4. Test Suite Boundaries

- **14 EditMode Tests Exist & Pass**: 12 specification acceptance tests (`DF-T01` to `DF-T12`) and 2 unit tests (`DF-T13`, `DF-T14`) execute and pass cleanly under Unity batchmode.
- **Zero PlayMode Tests Exist**: All automated tests operate within EditMode or batchmode test harnesses.
- **No Physical Device Assertion Runs**: Test execution occurs on the local authoring host machine.
