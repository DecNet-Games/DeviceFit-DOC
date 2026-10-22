---
layout: default
title: RegressionSuiteService
nav_order: 7
parent: API Reference
description: "Reference documentation for RegressionSuiteService in Decnet.DeviceFit.Editor."
permalink: /docs/api/regression-suite-service/
---

# `RegressionSuiteService` Class

```csharp
namespace Decnet.DeviceFit.Editor
public static class RegressionSuiteService
```

The `RegressionSuiteService` coordinates headless batchmode regression runs for continuous integration pipelines.

---

## Static Methods

```csharp
public static void RunHeadlessRegression()
```
The standard entry point invoked via command-line `-executeMethod`:
1. Parses command line parameters (`-experimentAsset`, `-reportOutput`, `-logFile`).
2. Loads the target scene and route definition.
3. Executes warm-up and 3 repeat passes.
4. Compares median results against the assigned `ExperimentBudget`.
5. Writes the HTML and JUnit XML reports.
6. Calls `EditorApplication.Exit(exitCode)` with deterministic codes (`0` for success, `1` for budget regression, `2` for context invalidation).

```csharp
public static GateReport EvaluateRegression(
    CaptureSession currentRun, 
    ExperimentBudget budget)
```
Evaluates a recorded `CaptureSession` against an `ExperimentBudget` and returns a `GateReport` containing pass/fail flags, regression deltas, and violation causes.
