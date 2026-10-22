---
layout: default
title: Apply & Restore
nav_order: 8
parent: Features Overview
description: "Durable change journaling, SHA256 pre-apply hashing, and conflict detection."
permalink: /docs/features/apply-and-restore/
---

# Reversible Apply & Restoration

One of the greatest fears developers face when optimizing quality settings is: *"What if these changes break something and we can't remember the original values?"*

DeviceFit eliminates this risk through **durable, cryptographically-hashed change journaling** (`ChangeJournalService.cs`).

```
Candidate Selected ──> Calculate SHA256 ──> Write Journal Entry ──> Apply Settings ──> Revert Anytime (1-Click)
```

---

## How the Change Journal Works

When you click **Apply Reviewed Config** in the Experiment Desk:

1. **Pre-Apply Hash Calculation**: DeviceFit reads the target asset file on disk (such as `UniversalRenderPipelineGlobalSettings.asset` or a texture meta file) and computes its SHA256 checksum.
2. **Journal Persistence**: A new record is appended to `Library/DeviceFit/ChangeJournal.json`:
   ```json
   {
     "changeId": "CHG-20261022-094120",
     "timestamp": "2026-10-22T09:41:20Z",
     "targetAssetPath": "Assets/Settings/URP-High.asset",
     "preApplySha256": "e3b0c44298fc1c149afbf4c8996fb92427ae41e4649b934ca495991b7852b855",
     "modifiedProperties": [
       { "name": "shadowDistance", "oldValue": 150.0, "newValue": 80.0 },
       { "name": "cascadeCount", "oldValue": 4, "newValue": 2 }
     ],
     "status": "APPLIED"
   }
   ```
3. **In-Place Modification**: Settings are written to the asset using serialized object transactions, followed by `AssetDatabase.SaveAssets()`.

---

## Source Conflict Detection (DF-T06)

What happens if an artist or level designer modifies the URP Asset *after* DeviceFit applied a candidate setting?

In `DF-T06_SourceConflict_BlocksBlindOverwrite`, when you click **Restore**:
1. DeviceFit re-computes the current SHA256 checksum of the target asset on disk.
2. If the current hash does not match either the `preApplySha256` or the post-apply state, DeviceFit detects an **external modification conflict**.
3. Blind overwrite is blocked, and the **Applied Changes Window** prompts the user with an actionable diff:
   `[Conflict] Asset modified externally by user. Restore blocked to prevent data loss. [Force Revert] [Cancel]`

---

## Crash & Interrupt Safety (DF-T07)

If Unity crashes or domain reload interrupts a multi-step apply operation:
- The journal status remains `PENDING_VERIFICATION`.
- Upon next editor launch, the journal detects the incomplete operation and offers safe automated rollback to the pre-apply state.
