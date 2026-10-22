---
layout: default
title: CandidatePlannerService
nav_order: 5
parent: API Reference
description: "Reference documentation for CandidatePlannerService in Decnet.DeviceFit.Editor."
permalink: /docs/api/candidate-planner-service/
---

# `CandidatePlannerService` Class

```csharp
namespace Decnet.DeviceFit.Editor
public static class CandidatePlannerService
```

The `CandidatePlannerService` generates a bounded list of candidate trial configurations targeting a diagnosed bottleneck.

---

## Static Methods

```csharp
public static CandidatePlan GeneratePlan(
    BottleneckVerdict verdict,
    AllowedTunableDomains allowedDomains,
    ExperimentBudget budget)
```

### Parameters
- `verdict`: The diagnostic outcome produced by `BottleneckAnalyzerService`.
- `allowedDomains`: A bitmask indicating whether shadows, LOD, render scale, or texture limits may be adjusted.
- `budget`: The target frame time and tail latency bounds.

### Return Value
Returns a populated `CandidatePlan` object containing bounded, orthogonal candidate permutations ranked by estimated performance yield and visual preservation priority.
