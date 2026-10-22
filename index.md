---
layout: default
title: Home
nav_order: 1
description: "DeviceFit — Measured Performance Tuning Lab for Universal Render Pipeline (URP) games in Unity."
permalink: /
---

# DeviceFit — Measured Performance Tuning Lab
{: .fs-9 }

Stop guessing which quality settings your game can afford.
{: .fs-6 .text-grey-dk-000 }

[Get Started Now]({{ site.baseurl }}/docs/getting-started/){: .btn .btn-primary .fs-5 .mb-4 .mb-md-0 .mr-2 }
[View on Unity Asset Store](https://assetstore.unity.com/packages/slug/421038){: .btn .fs-5 .mb-4 .mb-md-0 }

---

![DeviceFit Cover Banner]({{ site.baseurl }}/assets/images/01_Cover_1950x1300.png)
{: .mb-6 }

## Pain vs. Solution: The Real Cost of Guessing Quality

Most optimization workflows in Unity follow a broken loop: open quality settings, toggle a shadow cascade or lower render scale, glance at the Editor stats panel, and hope frame rates improve. When player builds stutter on target hardware, the team scrambles to guess which change caused the regression.

```
Traditional Guesswork:
Change Setting ──> Look at Editor Stats ──> Guess Impact ──> Ship ──> Player Regressions

DeviceFit Measured Lab:
Declare Target ──> Set Budget ──> Capture Baseline ──> Find Bottleneck ──> Trial Bounded Delts ──> Visual A/B Gate ──> Apply/Restore Journal ──> CI Regression Gate
```

**DeviceFit transforms performance tuning from guesswork into a disciplined experiment workflow.** It does not scan your assets with heuristic linters, and it never promises a magic FPS boost. Instead, it measures steady-state frame times with noise-floor awareness, isolates the dominant bottleneck (CPU, GPU, GC, or wait time), runs bounded candidate trials, enforces visual quality checkpoints, and journals every change with SHA256 hashes so any setting can be restored cleanly.

> **Key Rule of Measurement**  
> An experiment workflow measures what actually happens on your target scene. It does not synthesize optimistic numbers, and it treats sub-noise variations as inconclusive rather than claiming false victories.

---

## Core Capabilities at a Glance

| Stage | Capability | What It Solves |
|:---|:---|:---|
| **1. Declare Target** | **Environment Discovery** | Captures engine version, URP package, graphics API, and hardware capability counters before benchmarking. |
| **2. Target Budget** | **Budget Profiles** | Sets firm budgets for $p50$, $p95$, $p99$ frame times, spike budgets, and GC allocation limits per hardware tier. |
| **3. Capture Baseline** | **Multi-Repeat Route Recording** | Runs camera routes with warm-up stabilization and computes statistical medians over $\ge 3$ repeats. |
| **4. Identify Stage** | **Bottleneck Analyzer** | Classifies whether CPU main thread, render thread, GPU, GC pressure, or wait time dominates the frame budget. |
| **5. Plan Bounded Trials** | **Candidate Planner** | Generates combinatoric trial plans for shadows, cascades, LOD bias, render scale, and texture budgets. |
| **6. Verify Quality** | **Visual Comparison Gate** | Renders side-by-side and pixel difference heatmaps to protect visual truth before any change is accepted. |
| **7. Reversible Persist** | **Change Journal & Restorer** | Persists selected configurations with SHA256 pre-apply hashes; restores cleanly or detects external user edits. |
| **8. Automated Gate** | **CI Regression Runner** | Headless CLI batch runner that fails builds when a commit violates budget policies or regresses frame times. |

---

## Verified Facts & Platform Transparency

DeviceFit prioritizes measurement truth above marketing hype. Here is our verified operational fence:

```
[ Verified In Engine ]
✔ Developed and verified on Unity 6000.5.4f1 (compatible with Unity 6000.3 LTS baseline)
✔ Universal Render Pipeline (URP 17.5.0) target render pipeline
✔ 14/14 automated EditMode suite passed (12 spec acceptance tests DF-T01 to DF-T12 + 2 unit tests)
✔ DFT11 hot-path verified: strictly 0 heap bytes allocated per sample (CaptureSession / ProfilerHarness)
✔ Reversible texture asset importer overrides and SHA256 journal conflict detection verified

[ Explicitly NOT VERIFIED — No Device Claims ]
✖ Standalone Windows player (D3D11) execution has NOT been run on compiled binary
✖ Physical Android device (Vulkan) execution has NOT been run on attached hardware
✖ iOS and consoles are completely OUT OF SCOPE
✖ Zero PlayMode tests exist; zero claims of device-measured FPS are made
```

> **Platform Honesty Mandate**  
> DeviceFit provides target definitions and socket handshake protocols for Android Vulkan and Windows D3D11 development players. However, because no compiled player runs have taken place in this release cycle, all device and platform behavior is explicitly marked **NOT VERIFIED**. We never simulate or fabricate device numbers.

---

## Quick Navigation

- [Installation Guide]({{ site.baseurl }}/docs/installation/) — Install DeviceFit from the Unity Asset Store.
- [Getting Started]({{ site.baseurl }}/docs/getting-started/) — Understand the 11-step tuning loop and execute your first run.
- [Quick Start Guide]({{ site.baseurl }}/docs/quick-start/) — Jump straight into the Experiment Desk with the pre-built sample scenes.
- [Core Concepts]({{ site.baseurl }}/docs/concepts/the-tuning-loop/) — Deep dive into the tuning loop, noise floor, and evidence models.
- [Feature Reference]({{ site.baseurl }}/docs/features/) — Technical documentation of all 12 subsystems and Editor windows.
- [Sample Lab Scenes]({{ site.baseurl }}/docs/sample/lab-scenes/) — Explore the Courtyard GPU, Interaction CPU, and Texture Wall scenes.
- [Limitations & Honesty]({{ site.baseurl }}/docs/limitations/) — Detailed review of platform boundaries and out-of-scope capabilities.
