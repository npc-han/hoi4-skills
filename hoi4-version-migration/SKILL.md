---
name: hoi4-version-migration
description: Use when porting a HOI4 mod from an older game version to a newer one, encountering errors after a game update, or needing to check what modifiers, effects, or triggers were added, changed, or removed between versions. Also use when adapting a mod for compatibility with another major mod (e.g., NCNS compatibility)
---

# HOI4 Version Migration

## Overview

Each HOI4 major version changes the script API: adds new effects/modifiers, deprecates old ones, and changes behavior. Mods that worked on 1.14 may break silently on 1.15+. A single deprecated effect can cause silent failure — no entry in `error.log`, the effect simply doesn't fire.

**Core principle: Before porting, audit all effect and modifier uses against the target version's documentation.**

## Version Change References

### 1.17.3 vs 1.16.9
Reference: **hoi4-code-reference** → `references/1.17.3对比1.16.9新增修正（5改）.txt` (local copy bundled with the skill)

Key additions in 1.17:
- Army modifiers: `army_claim_attack_factor`, `army_retreat_speed_factor`
- Doctrine mastery gain: `<Doctrine>_mastery_gain_factor`, `<Doctrine>_track_mastery_gain_factor`
- Air invasion: `air_invasion_division_cap`, `air_invasion_preparation`
- Naval: `shore_bombardment_collateral_damage_factor`
- Faction: `faction_influence_contribution_factor`, `faction_influence_war_score_factor`

### v1.15.x Migration Guide
Reference: `本地资料库/版本适配指南\v1.15.x HOI 模组迁移（适配）工作指南.pdf`
Comprehensive coverage of all API changes, file structure changes, and common migration issues for the 1.15 jump.

### NCNS Compatibility Guide
Reference: `本地资料库/版本适配指南\NCNS兼容与适配指南（英汉对照版）.pdf`
Inter-mod compatibility patterns and best practices for ensuring mods work together.

## Migration Procedure

1. **Identify target version changes** — Read the version change reference for your source → target jump
2. **Audit effects/triggers** — Grep your mod for each deprecated or changed keyword. Use **hoi4-code-reference** to verify current syntax for each hit
3. **Check file structure** — Some versions change expected file paths (e.g., `common/characters/` added in 1.5 for character system, faction system restructured in 1.17)
4. **Test incrementally** — Enable mod on target version, watch `error.log`, fix one category at a time
5. **Silent failure audit** — Effects that still parse but do nothing are hardest to catch. Search for effect names that may have been split or renamed — cross-reference with **hoi4-code-reference**.

## Common Migration Pitfalls

| Symptom | Likely Cause |
|---|---|
| Focus effects don't fire | Deprecated effect name — check docs for current name |
| Missing character portraits | Character system restructured in 1.5+ — `common/characters/` format changed |
| Doctrine tree broken | Doctrine mastery system in 1.12+ — legacy setup needs rewrite |
| Equipment designer broken | Equipment module system changed in 1.13+ |
| Faction system broken | Faction system restructured in 1.17 — old `factions/` directory format deprecated |
| Silent modifier not applying | Modifier was renamed — old name parses but does nothing |
| `replace_path` causes missing database entries | `replace_path` removes ALL vanilla files in that directory — port over generic/base files your mod doesn't touch |
| CTD on loading screen | A `replace_path`'d directory is missing essential vanilla files that other systems reference — audit each `replace_path` entry |
| Localisation shows raw keys after migration | Language file format changed between versions or `.yml` encoding lost BOM during file operations |
| Naval OOB causes CTD on country select | Pre-1.12 carrier airwing format no longer supported — update to new airwing format |

## Version Change Summary

| Version | Key Changes Impacting Mods |
|---|---|
| **1.17** (Faction) | Faction system restructured: `common/factions/` with templates/goals/rules. New modifiers: `army_claim_attack_factor`, `<doctrine>_mastery_gain_factor`, `air_invasion_division_cap`. Marine modifiers renamed (`marines_*` → `marine_*`). |
| **1.16** (Inner Circle) | Inner circle system added. New `common/character_interaction_categories/`. |
| **1.15** (Special Projects) | Special projects system: `common/special_projects/`. New scope: `SPECIAL_PROJECT`. |
| **1.14** (MIO) | Military Industrial Organizations: `common/industrial_organizations/`. New scope: `MIO`. Equipment designer expanded. |
| **1.13** (Arms Against Tyranny) | Equipment modules restructured. New `common/units/equipment/modules/`. |
| **1.12** (By Blood Alone) | Doctrine mastery system. `add_mastery`/`add_mastery_bonus`. Extended flag syntax with `days`/`value`. New airwing format. |
| **1.11** (No Step Back) | Character system: `common/characters/` with roles. Supply system overhaul. |

## Cross-Reference

- **hoi4-code-reference**: Current effect/modifier syntax to compare against deprecated versions
- **hoi4-templates**: Templates reflect latest file format — compare your old files against current templates
- **hoi4-mod-workflow**: Dependency order for creating new files during migration
