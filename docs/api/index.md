---
layout: default
title: API Reference
nav_order: 11
has_children: true
permalink: /docs/api/
---

# Public API Reference

DeviceFit exposes a clean, modular API surface across its runtime contracts, tunable adapters, and editor services.

```
Decnet.DeviceFit Namespaces:
├── Decnet.DeviceFit (Contracts, Profiling, Network, Models)
├── Decnet.DeviceFit.Runtime (Lifecycle adapters, Profile Appliers, Tunables)
└── Decnet.DeviceFit.Editor (Services, Windows, Regression Runners)
```

---

## API Catalog

Explore the primary public types:

| Class / Struct | Namespace | Description |
|:---|:---|:---|
| [ProfilerHarness]({{ site.baseurl }}/docs/api/profiler-harness/) | `Decnet.DeviceFit` | High-precision profiler harness measuring CPU, GPU, memory, and frame timings with zero heap allocation. |
| [RouteRunner]({{ site.baseurl }}/docs/api/route-runner/) | `Decnet.DeviceFit` | Runtime component that drives the camera along authored route checkpoints and invokes fixture hooks. |
| [FixtureAdapter]({{ site.baseurl }}/docs/api/fixture-adapter/) | `Decnet.DeviceFit` | Abstract lifecycle base class for scene entities requiring readiness checks and state resets between benchmark runs. |
| [URPTunableAdapter]({{ site.baseurl }}/docs/api/urp-tunable-adapter/) | `Decnet.DeviceFit` | Reflection-free adapter to query, modify, and restore Universal Render Pipeline quality settings. |
| [CandidatePlannerService]({{ site.baseurl }}/docs/api/candidate-planner-service/) | `Decnet.DeviceFit.Editor` | Editor service that generates bounded trial plans based on diagnosed bottlenecks. |
| [ChangeJournalService]({{ site.baseurl }}/docs/api/change-journal-service/) | `Decnet.DeviceFit.Editor` | Cryptographically-hashed journal recording pre-apply SHA256 checksums and managing clean rollbacks. |
| [RegressionSuiteService]({{ site.baseurl }}/docs/api/regression-suite-service/) | `Decnet.DeviceFit.Editor` | Headless batchmode runner that executes automated regression gates in CI pipelines. |
