---
layout: default
title: Visual Comparison
nav_order: 7
parent: Features Overview
description: "Visual A/B comparisons, pixel difference heatmaps, and resolution transparency gates."
permalink: /docs/features/visual-comparison/
---

# Visual Comparison & Quality Gates

Saving 4 milliseconds of frame time is counterproductive if your game's visual presentation becomes unacceptably blurry or shadows disappear completely.

DeviceFit integrates a **Visual Quality Gate** (`CapturesComparisonWindow.cs`) into the decision loop.

```
Candidate Trial ──> Checkpoint Still Capture ──> Structural Dissimilarity ──> Side-by-Side Review ──> Apply Decision
```

---

## Checkpoint Still Captures

When authoring a camera route, you can mark specific waypoints as `isVisualCheckpoint = true`. During candidate trial runs:
1. The camera pauses at the designated checkpoint transform.
2. The scene renders a high-resolution uncompressed still frame.
3. The image is saved into the experiment capture directory with metadata recording the candidate ID and camera transform.

---

## Side-by-Side & Difference Heatmaps

![Visual Comparison Checkpoints]({{ site.baseurl }}/assets/images/09_Checkpoints_2400x1600.png)
{: .mb-4 }

In the **Captures & Comparisons** window (**Tools > Decnet > DeviceFit > Captures & Comparisons**), you can inspect:

1. **Split-Screen Wipe**: An interactive slider allowing you to wipe between the Baseline frame and Candidate frame.
2. **Absolute Pixel Difference Heatmap**: Highlights regions where color values deviate by $> 5\%$.
3. **Hero Mask Regions**: Focuses quality validation on central gameplay areas (e.g. player character or reticle) while allowing greater compression in peripheral regions.

---

## Resolution Transparency & Dimension Checks (DF-T08)

Lowering render scale (e.g. from 1.0 to 0.75) is an effective way to relieve fill-rate bottlenecks. However, comparing images of different resolutions with naive pixel difference algorithms produces artificial $100\%$ mismatch scores.

In `DF-T08_VisualTruth_InvalidDimensions_BlocksFalsePixelDiff`, DeviceFit enforces:
1. **Dimension Validation**: If baseline and candidate captures differ in pixel dimensions, false pixel-difference scores are blocked.
2. **Resolution Tradeoff Disclosure**: Any candidate that modifies render scale or dynamic resolution is explicitly labeled with a **Resolution Disclosure Notice** in reports and UI:
   `[Notice] Frame time reduction achieved by 15% render scale downsampling (1080p -> 918p).`
