---
layout: default
title: CI Regression Gate
nav_order: 11
parent: Features Overview
description: "How to run automated regression gates in CI pipelines to fail builds when frame times exceed budget."
permalink: /docs/features/ci-gate/
---

# Continuous Integration (CI) Regression Gate

Performance tuning is not a one-time task; it is an ongoing discipline. Without automated gates, optimization gains achieved today will slowly regress over months of active game production.

DeviceFit includes a headless, command-line CI runner (`RegressionSuiteService.cs`) that integrates directly into GitHub Actions, GitLab CI, or Jenkins.

```
Git Push / Pull Request ──> Headless Unity Batchmode ──> Run Benchmark Route ──> Evaluate Budget ──> Exit Code 0 (Pass) / 1 (Fail)
```

---

## Command-Line Invocation

To execute the automated regression suite in a headless CI environment:

```powershell
Unity.exe -batchmode -nographics -projectPath "C:\YourProject" `
  -executeMethod Decnet.DeviceFit.Editor.RegressionSuiteService.RunHeadlessRegression `
  -experimentAsset "Assets/Settings/Experiments/CI_Gate_Experiment.asset" `
  -reportOutput "C:\CI_Artifacts\DeviceFit_Report.html" `
  -logFile "C:\CI_Artifacts\unity_benchmark.log"
```

---

## Exit Codes & Build Gating

`GateReport.cs` returns deterministic exit codes to control CI pipeline progression:

| Exit Code | Gate Status | Meaning |
|:---:|:---|:---|
| **`0`** | **PASSED** | All metrics ($p50, p95, p99$, GC alloc) satisfied target budgets. |
| **`1`** | **BUDGET_REGRESSION** | One or more metrics exceeded budget limits (e.g. $p95$ frame time was 22.4ms vs 20.0ms budget). |
| **`2`** | **CONTEXT_INVALID** | Invariant context mismatch (e.g. wrong render pipeline asset or missing graphics device). |
| **`3`** | **ROUTE_ERROR** | Checkpoint not reached, scene missing `MainCamera`, or fixture reset failure. |

---

## Generating JUnit XML for CI Dashboards

In addition to HTML summaries, the CI runner outputs `TestResults.xml` compatible with GitHub Actions and GitLab test summary viewers:

```xml
<test-suite name="DeviceFit_RegressionGate" tests="4" failures="1">
  <test-case name="FrameTime_P50" result="Passed" time="14.2" />
  <test-case name="FrameTime_P95" result="Failed" time="22.4">
    <failure message="Frame time p95 (22.4ms) exceeded target budget (20.0ms)." />
  </test-case>
  <test-case name="GC_Alloc_HotPath" result="Passed" time="0" />
  <test-case name="Visual_Dissimilarity" result="Passed" time="0.02" />
</test-suite>
```
