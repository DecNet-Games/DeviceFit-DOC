---
layout: default
title: CI Pipeline Integration
nav_order: 4
parent: Workflows Overview
description: "Complete GitHub Actions workflow configuration to enforce DeviceFit budget gates on pull requests."
permalink: /docs/workflows/ci-pipeline/
---

# Continuous Integration Pipeline Setup

Automating DeviceFit inside your GitHub Actions CI pipeline ensures that no pull request introducing a framerate regression can be merged into `main`.

---

## Example GitHub Actions Workflow File

Create `.github/workflows/performance-gate.yml` in your repository:

```yaml
name: Performance Regression Gate

on:
  pull_request:
    branches: [ main ]

jobs:
  run-benchmark:
    runs-on: windows-latest
    steps:
      - name: Checkout Code
        uses: actions/checkout@v4
        with:
          lfs: true

      - name: Cache Unity Library
        uses: actions/cache@v4
        with:
          path: Library
          key: Library-Unity-6000-${{ hashFiles('Packages/manifest.json') }}

      - name: Execute DeviceFit Headless Regression Gate
        run: |
          & "C:\Program Files\Unity\Hub\Editor\6000.5.4f1\Editor\Unity.exe" `
            -batchmode `
            -nographics `
            -projectPath . `
            -executeMethod Decnet.DeviceFit.Editor.RegressionSuiteService.RunHeadlessRegression `
            -experimentAsset "Assets/Settings/CI_Gate_Experiment.asset" `
            -reportOutput "${{ github.workspace }}/Artifacts/DeviceFit_Report.html" `
            -logFile "${{ github.workspace }}/Artifacts/unity_ci.log"
        shell: powershell

      - name: Upload Benchmark HTML Report
        if: always()
        uses: actions/upload-artifact@v4
        with:
          name: devicefit-performance-report
          path: Artifacts/DeviceFit_Report.html
```

---

## Pipeline Behavior on Regressions

1. If the pull request code or assets cause the $p95$ frame time to exceed the experiment budget:
   - `RegressionSuiteService` prints the failure details to the CI log.
   - The Unity process exits with **code 1**.
   - GitHub Actions marks the pull request check as **Failed**, blocking merge.
2. If all frame times and GC allocations are within budget:
   - The runner exits with **code 0**.
   - The check passes, and the HTML performance report is attached as an artifact for review.
