---
layout: default
title: Candidate Trials
nav_order: 6
parent: Features Overview
description: "How CandidatePlannerService bounds setting combinations and evaluates quality tradeoffs."
permalink: /docs/features/candidate-trials/
---

# Candidate Trials & Planning

Once the primary bottleneck has been identified, `CandidatePlannerService.cs` formulates a bounded set of trial configurations.

---

## Why Bounded Permutations Matter

Modern render pipelines contain hundreds of exposed settings:
- Shadow resolution (512, 1024, 2048, 4096)
- Shadow distance (20m to 200m)
- Cascade count (1, 2, 3, 4)
- LOD bias (0.3 to 2.0)
- Render scale (0.5 to 1.0)
- Texture max size limits (512 to 4096)

Testing every combination of 10 settings at 3 levels each requires $3^{10} = 59,049$ benchmark runs. At 1 minute per run, that would take over 40 days of continuous testing.

DeviceFit solves this through **targeted, bounded planning**:
1. Only domains related to the diagnosed bottleneck are enabled.
2. Orthogonal setting deltas are chosen first (e.g. testing shadow cascades independently of render scale).
3. Extreme degradations (such as setting render scale to 0.5) are automatically avoided unless explicitly requested.

---

## Supported Tunable Domains

In `CandidatePlan.cs`, settings are grouped into four formal domains:

| Domain | Tunable Property | Typical Candidate Delta |
|:---|:---|:---|
| **Shadows** | `shadowDistance`, `shadowCascadeCount` | 150m $\to$ 80m; 4 cascades $\to$ 2 cascades |
| **Geometry / LOD** | `lodBias` | 1.0 $\to$ 0.75 |
| **Resolution** | `renderScale` | 1.0 $\to$ 0.85 (with disclosure notice) |
| **Textures** | Platform `maxTextureSize` override | 2048 $\to$ 1024 (via `TextureTrialBuildService`) |

---

## Interleaved Trial Execution

When executing candidate trials, `TrialRunnerService` uses an interleaved execution pattern:

```
[Trial Schedule]
1. Baseline Run (Verification)
2. Candidate A (Shadow Reduction)
3. Baseline Run (Thermal Check)
4. Candidate B (LOD Reduction)
5. Baseline Run (Thermal Check)
6. Candidate C (Render Scale Reduction)
```

By re-running the baseline between candidate runs, DeviceFit detects whether device thermal throttling is degrading later runs, ensuring that improvements are not obscured by temperature-induced clock drops.
