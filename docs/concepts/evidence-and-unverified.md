---
layout: default
title: Evidence & Unverified State
nav_order: 3
parent: Core Concepts
description: "Why DeviceFit marks platform cells as NOT VERIFIED when physical hardware runs do not exist."
permalink: /docs/concepts/evidence-and-unverified/
---

# Evidence Models and the "NOT VERIFIED" State

A core principle of DeviceFit is: **no claim is made without empirical, reproducible evidence.**

In game development tooling, vendors frequently claim "supports Android, iOS, Windows, Mac, and Consoles" simply because the code compiles without syntax errors on those platforms. DeviceFit rejects this practice. Compiling C# code in the Unity Editor does not prove that hardware GPU counters work on an Android Vulkan driver, or that FrameTimingManager behaves identically in a Windows D3D11 player.

---

## The Classification Taxonomy

Every capability in DeviceFit carries one of three explicit verification labels:

| Verification Status | Meaning | Operational Boundary |
|:---|:---|:---|
| **LOGIC-VERIFIED (EditMode)** | Algorithms, contracts, and math executed and passed under Unity batchmode NUnit runner. | Tested on real host machine under Unity 6000.5.4f1. Real disk IO and asset importer overrides verified. |
| **PARTIALLY VERIFIED** | Core logic passes, but platform hardware confirmation is pending. | Example: `DF-F23` (Automated Verification) passes 12/12 EditMode acceptance tests, but lacks physical device runs. |
| **NOT VERIFIED** | Target hardware or runtime environment has not been executed on real silicon or compiled player binary. | Standalone Windows D3D11 player runs and Android Vulkan physical device runs. |

---

## Ground Truth Test Evidence (14/14 Tests Passed)

During development and release auditing on **Unity 6000.5.4f1**, the complete automated test suite was executed in batchmode:

```powershell
Unity.exe -batchmode -nographics -runTests -projectPath "..." -testPlatform EditMode -testResults "C:\MarkBridge\df-tests.xml"
```

### Spec Acceptance Tests (DF-T01 through DF-T12)
1. **DF-T01 (Missing Counters)**: Unsupported GPU counters report `null`, never `0.0`.
2. **DF-T02 (Invalid Route)**: Empty checkpoints or missing camera stops execution with clear cause.
3. **DF-T03 (Repeat Noise)**: Variations below $\max(0.5\text{ms}, 2\%)$ do not claim material improvement.
4. **DF-T04 (Context Mismatch)**: Editor vs Player or mismatched graphics APIs flagged as `Incomparable`.
5. **DF-T05 (Texture Rebuild)**: Live texture importer overrides max size to 64 and restores original 2048 cleanly.
6. **DF-T06 (Source Conflict)**: External user edits detected via SHA256; blind overwrite blocked.
7. **DF-T07 (Interrupted Apply)**: Incomplete multi-step apply journal recovers safely on restart.
8. **DF-T08 (Visual Truth)**: Mismatched image dimensions block false pixel score calculations.
9. **DF-T09 (Unavailable Budget)**: Over-budget candidates report budget failure without fake success.
10. **DF-T10 (Owned Connection)**: Mismatched player build hashes reject stale telemetry data.
11. **DF-T11 (Harness Overhead)**: Hot-path per-frame sampling verified at strictly **0 bytes heap allocation**.
12. **DF-T12 (End-to-End Workflow)**: Complete UX pipeline executed from baseline to CI report.

### Unit Tests (DF-T13 & DF-T14)
- **DF-T13 (FixtureAdapter Contract)**: Readiness, state reset, and checkpoint fence lifecycle verified.
- **DF-T14 (MetricSeries Calculations)**: Mathematical accuracy of $p50, p95, p99$, mean, and standard deviation verified.

---

## Why Platform Cells Remain "NOT VERIFIED"

While all 14 tests pass cleanly in the Unity Editor:
1. **No Standalone Windows D3D11 Player Build Was Run**:
   While the protocol and socket code exist, no compiled `.exe` player run was recorded on this machine.
2. **No Physical Android Vulkan Device Was Attached**:
   While Android target definitions and socket communication protocols exist, no physical handset was connected over ADB to measure real mobile Vulkan GPU timings.

Therefore, in our state documents and feature matrix, all platform-dependent cells (DF-F01, DF-F03, DF-F05, DF-F06, DF-F11, DF-F14, DF-F18) are transparently marked:
`NOT VERIFIED — EditMode logic tests over simulated data only`.

> **Commitment to Truth**  
> We never fabricate device telemetry, fake mobile benchmarks, or claim platforms we haven't personally plugged into a USB cable and verified.
