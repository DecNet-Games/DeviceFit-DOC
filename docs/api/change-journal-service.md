---
layout: default
title: ChangeJournalService
nav_order: 6
parent: API Reference
description: "Reference documentation for ChangeJournalService in Decnet.DeviceFit.Editor."
permalink: /docs/api/change-journal-service/
---

# `ChangeJournalService` Class

```csharp
namespace Decnet.DeviceFit.Editor
public static class ChangeJournalService
```

The `ChangeJournalService` manages durable change persistence, pre-apply SHA256 hashing, rollback execution, and external modification conflict detection.

---

## Static Methods

```csharp
public static string ApplyCandidate(CandidateSetting candidate, UnityEngine.Object targetAsset)
```
Calculates the target asset's current SHA256 checksum, appends an entry to `Library/DeviceFit/ChangeJournal.json`, mutates the asset properties, and saves changes to disk. Returns the unique `changeId`.

```csharp
public static bool RestoreChange(string changeId, out string errorMessage)
```
Validates the target file's current SHA256 hash. If unchanged, restores pre-apply values. If an external user modification is detected, sets `errorMessage` and returns `false` to block blind overwrite.

```csharp
public static List<ChangeJournalEntry> GetActiveEntries()
```
Returns all recorded journal entries currently in the applied state.
