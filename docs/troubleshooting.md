---
layout: default
title: Troubleshooting
nav_order: 9
parent: null
description: "Resolving common setup issues, GPU timer unavailability, and route errors in DeviceFit."
permalink: /docs/troubleshooting/
---

# Troubleshooting Common Issues

This guide addresses common errors and warnings encountered when running benchmarks in DeviceFit.

---

## 1. GPU Frame Time Reports "Unavailable" (null)

### Symptoms
In the Experiment Desk or raw CSV exports, `GpuTimeMs` displays `-` or empty instead of a numeric value.

### Cause
In the Unity Editor, `FrameTimingManager` GPU timestamps are disabled by the Unity engine core to prevent driver stalls. This is standard Unity engine behavior.

### Resolution
- In-Editor benchmarks will only measure CPU Main Thread, Render Thread, and GC memory metrics.
- As verified in `DF-T01_MissingCounters_RemainNull_NeverReportZero`, DeviceFit deliberately reports `null` rather than guessing a fake `0.0ms` value.
- To measure true GPU raster and shading time, pair the Experiment Desk with a **Standalone Development Player** where `FrameTimingManager` is supported by the graphics driver.

---

## 2. "Invalid Route: Missing Waypoints or Camera" (DF-T02)

### Symptoms
When clicking **Capture Baseline**, execution halts immediately with an error dialog: `Benchmark run invalidated: Route contains no active checkpoints or scene is missing MainCamera.`

### Resolution
1. Verify that your active scene contains a Camera tagged `MainCamera`.
2. Open your assigned `RouteDefinition.asset` in the Route Authoring Window.
3. Ensure the route contains at least 2 checkpoints with non-zero durations.
4. Verify that any assigned `FixtureAdapter` components implement `AssertReadiness()` without throwing exceptions.

---

## 3. "Restore Blocked: External Modification Conflict" (DF-T06)

### Symptoms
When clicking **Restore** in the Applied Changes window, an alert appears: `Source conflict detected. File was edited externally.`

### Resolution
1. DeviceFit calculates SHA256 checksums before applying any changes. If an artist or designer subsequently modified the URP Asset or texture importer settings, DeviceFit detects the altered hash.
2. If you are confident you want to discard the external edits, click **Force Restore**.
3. To preserve the external edits, click **Cancel** and manually reconcile the settings in the Inspector.

---

## 4. "Socket Connection Timeout" (Player Pairing)

### Symptoms
Connecting to `127.0.0.1:9090` in the Player Connection Window hangs and times out.

### Resolution
1. If connecting to a mobile device via USB, verify that ADB port forwarding is active:
   `adb forward tcp:9090 tcp:9090`
2. Verify that your player build was created with **Development Build** and **Autoconnect Profiler** checked.
3. Check your Windows Firewall settings to ensure inbound connections on port 9090 are permitted.
