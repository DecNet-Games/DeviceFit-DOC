---
layout: default
title: Device Targets
nav_order: 9
parent: Features Overview
description: "Hardware target matrices (Windows D3D11 / Android Vulkan) — targets and sockets, NOT verified claims."
permalink: /docs/features/device-targets/
---

# Device Targets & Remote Connections

DeviceFit provides target definitions and TCP socket connection protocols to pair the Unity Editor with standalone development players on local networks or tethered mobile devices.

```
Unity Editor (Experiment Desk)  <──── TCP Socket (Port 9090) ────>  Standalone Development Player
[Send Benchmark Request]                                             [Record Hardware Vulkan Frames]
[Receive Telemetry Stream]      <──────────────────────────────────  [Send FrameTimingManager Metrics]
```

---

## Target Matrix: Specifications vs. Verification

To maintain strict truth in documentation, we distinguish between **Target Specifications** (what the software is designed to interface with) and **Verified Runs** (what was executed and proven on physical silicon in this release).

| Target Tier | Planned OS / API | Socket Handshake Protocol | Physical Run Status |
|:---|:---|:---|:---|
| **Windows Desktop** | Windows 10/11 x64 / Direct3D 11 | Implemented (`PlayerConnectionProtocol.cs`) | **NOT VERIFIED** (No player binary built/run) |
| **Android Mobile** | Android 12+ / Vulkan 1.2+ | Implemented (`adb forward tcp:9090`) | **NOT VERIFIED** (No physical device connected) |
| **iOS / Consoles** | Metal / Proprietary | None | **OUT OF SCOPE** (Unsupported) |

> **Platform Honesty Disclosure**  
> We explicitly state: **No Windows-player (D3D11) run and no Android-device (Vulkan) run exist in this release.** All platform and device behavior is labeled **NOT VERIFIED**. Do not interpret the presence of socket protocols or Android target definitions as a guarantee of device-measured numbers.

---

## The Handshake Protocol (DF-T10)

When pairing with an external player, `PlayerConnectionProtocol.ValidateHandshake()` enforces three security and integrity checks:

1. **Protocol Version Match**: Rejects incompatible wire protocols.
2. **Build ID Match**: If the player was built from an older commit or different asset state, the handshake is rejected with `Player build mismatch`. This prevents merging telemetry from stale builds.
3. **Scenario ID Match**: Ensures the player is loaded into the exact same benchmark scene as the Editor controller.
