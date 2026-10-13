# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

A RimWorld mod (Harmony-patched C# DLL) that remembers the resource readoute view when it is in "category" mode, either in a per-save or global manner. Also includes a right-click menu to expand or contract the resource readout. Targets RimWorld 1.5/1.6. The mod's package ID is `frenchua.ExpandResourceReadout`; it depends on the Harmony mod (`brrainz.harmony`).

This repo *is* the mod folder — it's meant to be cloned directly into a RimWorld `Mods/` directory (see `README.md`), not built and copied elsewhere.

This mod previously had a much more narrow focus of simply auto-expanding the resource readout view when a save game loads.

## Build

```
dotnet build
```

The project multi-targets one framework per RimWorld version, and each build drops its DLL/PDB into the version folder RimWorld's mod loader picks automatically:

- `net472` → RimWorld 1.6 (Unity/Mono) → `1.6/Assemblies/`. References come from NuGet (`Krafs.Rimworld.Ref`, pinned).
- `net10.0` → RimWorld 1.7 (custom "Waylith" CoreCLR, .NET 10) → `1.7/Assemblies/`. No 1.7 `Krafs.Rimworld.Ref` exists yet, so this references `Assembly-CSharp.dll` and `UnityStubs.dll` straight from the game install (defaults to two folders up from the repo; override with `-p:RimWorld17Dir=...`).

Build a single version with `dotnet build -f net472` or `-f net10.0`. Both share `Lib.Harmony.Ref` 2.4.2. There is no separate install/copy step — building in place is the deployment. There is no test suite — verification is done by loading the mod in RimWorld itself.

## Architecture

Three moving pieces, all in `Source/`:

- **`Mod.cs`** — the `Verse.Mod` entry point. On construction it runs `Harmony(HarmonyId).PatchAll()`, which auto-discovers and applies every `[HarmonyPatch]` in the assembly (currently the two files in `Source/Patches/`). `HarmonyId` is `com.jdfrench.RimWorldExpandResourceReadout`.
- **`Main.cs`** — defines `ExpandResourceReadoutComponent`, a `GameComponent` that walks the `ThingCategoryDef` tree (rooted at defs where `resourceReadoutRoot` is true) and recursively opens/closes every `TreeNode_ThingCategory` via `SetOpen(TreeOpenMasks.ResourceReadout, …)`. `OpenAll()` runs automatically on `LoadedGame()`/`StartedNewGame()`, and is also called directly by the context-menu patches below. `ExpandResourceReadoutSettings` exists but is currently an empty `ModSettings` stub (see README TODOs — a real settings dialog and persisted expand/collapse state are not implemented yet).
- **`Source/Patches/`** — Harmony postfix patches on the vanilla `RimWorld.Listing_ResourceReadout` class:
  - `RimWorld_Listing_ResourceReadout_DoCategory.cs` patches `DoCategory` (category rows).
  - `RimWorld_Listing_ResourceReadout_DoThingDef.cs` patches `DoThingDef` (individual resource rows).
  
  Both patches reimplement the row's clickable `Rect` from private/protected members of `Listing_ResourceReadout` (`curY`, `lineHeight`, the `LabelWidth` property, `XAtIndentLevel()`) via `HarmonyLib.AccessTools`, since those aren't exposed publicly. On a right-click (`MouseDown`, button 1) inside that rect, they consume the event and show a `FloatMenu` with Expand All / Close All, which call back into `Current.Game.GetComponent<ExpandResourceReadoutComponent>()`.

When touching these patches, keep the reflection-derived `Rect` math identical between the two files unless the underlying vanilla method's layout changes — they're duplicated on purpose (one per patched method) rather than shared, so check both when adjusting hit-testing or menu behavior.

## Mod metadata

`About/About.xml` declares the package ID, supported versions, and the Harmony dependency. Bump `modVersion` there and `VersionPrefix` in the `.csproj` together when cutting a release.
