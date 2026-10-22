---
layout: default
title: Reports & Exports
nav_order: 10
parent: Features Overview
description: "Portable HTML executive summaries and CSV raw frame exports for performance reviews."
permalink: /docs/features/reports/
---

# Reports & Raw Metric Exports

Clear communication is critical when presenting optimization proposals to lead programmers, technical directors, and studio producers.

DeviceFit generates both **executive HTML summaries** and **unaggregated CSV exports** via `ReportExportService.cs`.

---

## 1. Portable Single-File HTML Reports

Clicking **Export Report** in the Experiment Desk produces a self-contained, responsive HTML file that can be opened in any web browser without external server dependencies or cloud connections:

```
Report Sections:
├── Executive Summary (Target Environment, Budget Status, Verdict)
├── Metric Comparison Table (Baseline vs Candidate deltas for p50, p95, p99, GC)
├── Bottleneck Evidence (CPU/GPU timeline graphs, confidence ratings)
├── Visual A/B Side-by-Side (Embedded high-res checkpoint captures)
└── Change Ledger (Exact list of modified properties with pre/post values)
```

### Key Elements of the HTML Report
- **Color-Coded Status Pills**: Highlights whether the trial passed all budget constraints (`PASSED`, `MARGINAL`, or `FAILED`).
- **Resolution Tradeoff Notices**: Discloses any downsampling or render scale compromises.
- **SHA256 Checksums**: Lists asset hashes for provenance and audit tracking.

---

## 2. Raw Frame CSV Exports

For deep analysis in Microsoft Excel, Google Sheets, or Python/Pandas, DeviceFit exports every individual recorded frame to CSV:

```csv
FrameIndex,TimestampSeconds,CheckpointId,IsWarmup,CpuFrameTimeMs,CpuMainThreadMs,CpuRenderThreadMs,GpuTimeMs,GcAllocBytes,TotalAllocatedMemoryMB
0,0.000,start,1,22.1,18.4,4.2,21.9,0,412.5
1,0.016,start,1,21.8,18.1,4.1,21.5,0,412.5
...
100,1.642,gate_pass,0,16.2,12.1,3.8,15.9,0,412.8
```

- **Null-Preserving Semantics**: If a profiler counter (such as GPU frame time in the Editor) is unavailable, the CSV cell is output as empty, never synthesized as `0.0`.
- **Warmup Tagging**: The `IsWarmup` column allows you to filter out initial asset-loading transients during custom data processing.
