---
layout: default
title: FAQ
nav_order: 10
parent: null
description: "Frequently asked questions regarding DeviceFit licensing, technical scope, and measurement policies."
permalink: /docs/faq/
---

# Frequently Asked Questions

---

### Is DeviceFit available for free on GitHub?
**No.** DeviceFit is a premium commercial asset available exclusively on the **Unity Asset Store**. This documentation site provides reference guides and tutorials for licensed users.

---

### Does DeviceFit automatically increase my game's FPS?
**No.** DeviceFit is an experiment and measurement lab, not a code optimizer. It helps you measure performance accurately, identify bottlenecks, test bounded candidate quality settings, and catch regressions in CI. If your game is fundamentally limited by unoptimized gameplay code or bad shaders, changing quality settings may not help.

---

### Can I run DeviceFit without the Universal Render Pipeline (URP)?
**No.** DeviceFit's runtime tunable adapters (`URPTunableAdapter.cs`) and sample scenes interface directly with URP assets and volume components. Built-in Render Pipeline (BiRP) and HDRP are not supported.

---

### Why does GPU time display as "null" when testing inside the Editor?
The Unity Editor disables `FrameTimingManager` GPU timestamps internally. Rather than fabricating a fake 0.0ms metric, DeviceFit reports `null` (as verified in test `DF-T01`). True GPU timings require pairing with a standalone development player build.

---

### What is the measurement noise floor?
Run-to-run variance caused by operating system background tasks, CPU frequency boost clocks, and thermal variations creates jitter. DeviceFit enforces a mathematical noise threshold of $\max(0.5\text{ ms}, 2\%)$. Any performance delta smaller than this threshold is classified as inconclusive, preventing false victory claims.

---

### What are the verified facts for this release?
- Verified and tested under **Unity 6000.5.4f1**.
- **14/14 automated EditMode tests pass** (12 spec acceptance tests + 2 unit tests).
- Per-frame sampling hot path is verified at **strictly 0 bytes heap allocation**.
- Standalone player and physical Android hardware runs have not taken place and are honestly labeled **NOT VERIFIED**.
