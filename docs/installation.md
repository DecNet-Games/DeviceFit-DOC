---
layout: default
title: Installation
nav_order: 2
parent: null
description: "How to purchase, download, and install DeviceFit from the Unity Asset Store."
permalink: /docs/installation/
---

# Installing DeviceFit

DeviceFit is a commercial product distributed exclusively through the **Unity Asset Store**. It is not an open-source library or available as a free repository on GitHub.

```
Purchase on Asset Store ──> Unity Package Manager (My Assets) ──> Download & Import ──> Package Initialization
```

---

## Prerequisites

Before importing DeviceFit into your project, ensure your environment meets the minimum baseline:

| Component | Minimum Requirement | Verified Test Target |
|:---|:---|:---|
| **Unity Engine** | Unity 6000.3 LTS or higher | Unity 6000.5.4f1 |
| **Render Pipeline** | Universal Render Pipeline (URP) | URP 17.5.0 |
| **Scripting Backend** | Mono or IL2CPP (.NET Standard 2.1) | .NET Standard 2.1 |
| **Authoring OS** | Windows 10/11 64-bit | Windows 11 Pro 64-bit |

> **Universal Render Pipeline Required**  
> DeviceFit's runtime tunables (`URPTunableAdapter`) and sample scenes interface specifically with URP volume components, shadow cascades, and UniversalRenderPipelineAsset settings. Built-in Render Pipeline (BiRP) and High Definition Render Pipeline (HDRP) are unsupported.

---

## Step 1: Purchasing & Adding to My Assets

1. Visit the [Unity Asset Store](https://assetstore.unity.com/) and search for **DeviceFit — Measured Performance Tuning Lab**.
2. Complete your purchase under your Unity ID organization.
3. Click **Add to My Assets** to link the license to your Unity account.

---

## Step 2: Downloading via Unity Package Manager

1. Open your Unity project in the Unity Editor.
2. Navigate to the top menu: **Window > Package Manager**.
3. In the Package Manager dropdown (top-left), select **Packages: My Assets**.
4. In the search box, type `DeviceFit`.
5. Select the package and click **Download** in the bottom-right corner.
6. Once the download completes, click **Import**.

```
┌─────────────────────────────────────────────────────────────────┐
│ Package Manager                                          [-] [x]│
├─────────────────────────────────────────────────────────────────┤
│ Packages: My Assets ▼ | Filter: DeviceFit                       │
│                                                                 │
│ [Icon] DeviceFit — Measured Performance Tuning Lab  v1.0.0      │
│        Decnet Games • Released October 2026                     │
│                                                                 │
│                                           [ Re-Download ] [Import]│
└─────────────────────────────────────────────────────────────────┘
```

---

## Step 3: Selecting Import Assets

When the Unity **Import Unity Package** dialog appears, review the asset manifest:

- `Assets/Decnet/DeviceFit/Runtime/` — Core capture harness, domain contracts, profile appliers, and URP adapters.
- `Assets/Decnet/DeviceFit/Editor/` — Editor services, windows (Experiment Desk, Comparison, Route Authoring), and launchers.
- `Assets/Decnet/DeviceFit/Samples/` — Ready-made benchmark scenes (Courtyard GPU, Interaction CPU, Texture Wall) and test textures.
- `Assets/Decnet/DeviceFit/Documentation/` — Offline Markdown references and API notes.
- `Assets/Decnet/DeviceFit/Tests/` — Automated EditMode NUnit acceptance suite (`AcceptanceTests.cs`).

Ensure all items are checked and click **Import**.

---

## Step 4: Verifying Assembly Definitions & First Launch

DeviceFit uses strict assembly definition boundaries (`.asmdef`) to ensure zero Editor code leaks into player builds:

- `Decnet.DeviceFit.Runtime.asmdef`
- `Decnet.DeviceFit.Editor.asmdef` (Editor-only, references Runtime)
- `Decnet.DeviceFit.Tests.asmdef` (Editor-only, references NUnit and TestFramework)

After compilation finishes:
1. Open the top menu: **Tools > Decnet > DeviceFit > Welcome & Onboarding**.
2. The welcome wizard checks your active render pipeline, verifies project dependencies, and offers one-click generation of the sample scenes.
3. Alternatively, launch **Tools > Decnet > DeviceFit > Experiment Desk** (`Ctrl+Alt+D` on Windows) to inspect the main tuning hub.

> **Assembly Cleanliness**  
> If your project uses custom assembly definitions, add a reference to `Decnet.DeviceFit.Runtime` in any assembly that implements custom `FixtureAdapter` components or calls `DeviceProfileApplier`.
