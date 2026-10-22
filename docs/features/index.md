---
layout: default
title: Features Overview
nav_order: 5
has_children: true
permalink: /docs/features/
---

# Feature Reference

DeviceFit provides 12 specialized subsystems and dedicated Unity Editor windows designed to support every phase of empirical performance optimization.

```
DeviceFit Architecture:
├── Contracts & Data Models (ExperimentDefinition, RouteDefinition, CaptureSession, CandidatePlan)
├── Runtime Profiling (ProfilerHarness, RouteRunner, FixtureAdapter, URPTunableAdapter)
├── Editor Services (BottleneckAnalyzer, CandidatePlanner, ChangeJournal, TextureTrialBuild, RegressionSuite)
└── Editor Windows (ExperimentDesk, PlayerConnection, RouteAuthoring, CapturesComparison, AppliedChanges)
```

---

## Subsystem Catalog

Explore each subsystem in detail:

| Feature Page | Responsibility |
|:---|:---|
| [Environment Setup]({{ site.baseurl }}/docs/features/environment-setup/) | Declares Unity version, URP package, graphics API, and hardware counter availability before benchmarking. |
| [Budget Profiles]({{ site.baseurl }}/docs/features/budget-profiles/) | Configures target frame time, tail latency ($p95/p99$), and heap allocation budgets per target tier. |
| [Capture Sessions]({{ site.baseurl }}/docs/features/capture-sessions/) | Explains the `RawFrameRecord` value struct and `CaptureSession` multi-repeat median aggregation. |
| [Baseline Capture]({{ site.baseurl }}/docs/features/baseline-capture/) | Covers route warm-up stabilization, repeat runs, and noise filtering. |
| [Bottleneck Analysis]({{ site.baseurl }}/docs/features/bottleneck-analysis/) | Automatic classification of CPU main thread, render thread, GPU, or GC bottlenecks. |
| [Candidate Trials]({{ site.baseurl }}/docs/features/candidate-trials/) | Combinatoric planning of shadow distances, cascade counts, LOD bias, render scale, and texture limits. |
| [Visual Comparison]({{ site.baseurl }}/docs/features/visual-comparison/) | Side-by-side still captures, pixel difference heatmaps, and resolution tradeoff disclosure. |
| [Apply & Restore]({{ site.baseurl }}/docs/features/apply-and-restore/) | Persistent change journaling with SHA256 pre-apply hashes and conflict detection against external edits. |
| [Device Targets]({{ site.baseurl }}/docs/features/device-targets/) | Target hardware matrices (Windows D3D11 / Android Vulkan) — targets and sockets, NOT verified claims. |
| [Reports & Exports]({{ site.baseurl }}/docs/features/reports/) | Standalone portable HTML executive summaries and CSV raw frame metric exports. |
| [CI Regression Gate]({{ site.baseurl }}/docs/features/ci-gate/) | Headless batchmode runner that fails builds on performance regressions with standardized exit codes. |
| [Harness Overhead]({{ site.baseurl }}/docs/features/harness-overhead/) | Technical breakdown of `DFT11`, the strict 0-byte heap hot-path fix, and benchmark methodology. |
