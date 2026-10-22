---
layout: default
title: Android Target Tuning
nav_order: 3
parent: Workflows Overview
description: "Defining and budgeting Android Vulkan targets — configuration and socket targets only; device behavior is NOT VERIFIED."
permalink: /docs/workflows/android-target-tuning/
---

# Android Target Tuning (Target Setup Only)

Mobile game development demands strict thermal and power awareness. Mobile GPUs throttle rapidly when driven at high resolutions or heavy shadow loads.

This workflow details how to configure Android Vulkan target budgets and set up remote socket pairing.

> **CRITICAL PLATFORM HONESTY NOTICE**  
> As documented across this site, **NO physical Android device (Vulkan) runs have been executed or verified in this release**. The workflows described below cover configuration models, target budgets, and socket pairing protocols only. Device behavior is explicitly **NOT VERIFIED**. We make no claims of device-measured framerates on mobile silicon.

---

## 1. Defining the Android Budget Profile

Mobile devices cannot sustain high-end desktop thermal dissipation. An Android target budget should be configured conservatively:

```csharp
var mobileBudget = new ExperimentBudget
{
    targetFrameTimeMs = 33.33f, // 30 FPS target
    p95FrameTimeMs = 40.0f,     // 25 FPS hitch threshold
    p99FrameTimeMs = 50.0f,     // Emergency hitch ceiling
    maxGcAllocPerFrame = 0      // Zero GC allocation on hot loop
};
```

---

## 2. Tethering & Socket Port Forwarding

DeviceFit connects to external players using local TCP socket transport on port `9090`:

1. Build an **Android Development Player** with **Autoconnect Profiler** and **Script Debugging** enabled.
2. Connect your Android device to your development workstation via USB.
3. Enable USB Debugging in developer options.
4. Forward the TCP port over ADB:
   ```powershell
   adb forward tcp:9090 tcp:9090
   ```
5. Launch the development player on the Android device.

---

## 3. Pairing via Player Connection Window

1. In the Unity Editor, open **Tools > Decnet > DeviceFit > Connect Player**.
2. Enter the target IP (`127.0.0.1` for ADB-forwarded connections) and Port (`9090`).
3. Click **Connect to Target Player**.
4. Review the device descriptor:
   - Device Model & Operating System
   - Active Graphics API (must report `Vulkan`)
   - Build Hash Check (verifies player matches active project assets)
5. Once paired, trial commands sent from the Experiment Desk will execute on the player and stream telemetry back to your Editor.
