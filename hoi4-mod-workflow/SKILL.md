---
name: hoi4-mod-workflow
description: Use when starting a new HOI4 mod project from scratch, adding a major feature to an existing mod, or unsure about the correct order of operations for creating interconnected mod files. Also use when you need to understand how HOI4 mod file types depend on each other and what order to create them in
---

# HOI4 Mod Workflow

## Overview

HOI4 modding requires creating files in a specific dependency order: each file type references IDs defined in others. Wrong order creates broken references and difficult-to-diagnose silent failures.

**Core principle: Define before you reference.** Create dependency files first, then the files that consume them.

## Mod File Structure

Every HOI4 mod lives in `Documents/Paradox Interactive/Hearts of Iron IV/mod/` and consists of:

### Required Files

| File | Location | Encoding | Purpose |
|---|---|---|---|
| **`.mod` file** | `mod/` directory (outer) | UTF-8 with BOM | Tells the launcher how to handle the mod |
| **`descriptor.mod`** | Inside mod folder | UTF-8 **without** BOM | Same metadata, read by the game engine |
| **Mod data folder** | `mod/<modname>/` | — | Mirrors game directory structure |

### .mod File Format

```
name="My Mod Name"
path="mod/mymod"
tags={
    "Alternative History"
}
picture="thumbnail.png"
supported_version="1.17.*"
version="1.0"
```

Key fields:
- `name` — Display name shown in launcher
- `path` — Relative path to mod folder. **Must use forward slashes `/`**, NOT backslashes `\`. Backslashes are interpreted as escape characters and cause silent failures.
- `archive` — For Steam Workshop zip mods (instead of `path`)
- `dependencies` — Sub-mod requirement: `dependencies = { "Main Mod Name" }`. Must match the parent mod's `name` character-for-character.
- `user_dir` — For total conversions: gives the mod its own save storage
- `replace_path` — Completely ignore vanilla files in a directory. Use sparingly — always port over generic/base files to avoid missing database entries.
- `supported_version` — Target game version. Use `*` as wildcard: `"1.17.*"`. Must have exactly 2 dots (`"1.17.3"`, never `"1.17.3.4"`).
- `remote_file_id` — Steam Workshop ID (auto-generated on publish)

### descriptor.mod

Placed **inside** the mod folder. Contains the same fields as the outer `.mod` file **except `path`**. Must be encoded in **UTF-8 without BOM** (unlike the outer `.mod` file).

**Critical rule**: Both files must be edited for `replace_path` entries to take effect.

### Directory Structure

Your mod folder mirrors the game's internal structure:

```
mymod/
├── descriptor.mod
├── thumbnail.png
├── common/
│   ├── national_focus/
│   ├── countries/
│   ├── ideas/
│   ├── characters/
│   ├── decisions/
│   ├── scripted_guis/
│   ├── scripted_effects/
│   ├── scripted_triggers/
│   ├── defines/          ← .lua files
│   └── ...
├── events/
├── history/
│   ├── countries/
│   ├── states/
│   └── units/
├── localisation/         ← .yml files
├── interface/            ← .gui and .gfx files
├── gfx/
│   ├── flags/
│   ├── leaders/
│   └── ...
└── map/
```

### Mod Types

| Type | Characteristics | When to use |
|---|---|---|
| **Minor Mod** | Simple changes, no `user_dir` or `replace_path` | Small tweaks, single-country overhauls |
| **Major Mod / Total Conversion** | Uses `user_dir` for separate saves, `replace_path` to ignore vanilla | Full overhauls, alternate history total conversions |
| **Sub-Mod** | Uses `dependencies = { "Parent Mod" }` to load after parent | Compatibility patches, add-ons for existing mods |

## File Encoding Rules

**Incorrect encoding is one of the most common causes of silent failure and CTD.**

| File Type | Encoding | Critical Rule |
|---|---|---|
| `.txt` (all script files) | **UTF-8 without BOM** | Game silently skips malformed files |
| `.yml` (localisation) | **UTF-8 with BOM** | Missing BOM = all strings show as raw keys |
| `descriptor.mod` | **UTF-8 without BOM** | BOM here crashes the launcher |
| `.mod` (outer) | **UTF-8 with BOM** | — |
| `.lua` (defines) | **UTF-8 without BOM** | Extra commas crash the game |
| `.gui` / `.gfx` | **UTF-8 without BOM** | — |

### How to set encoding in editors:
- **VS Code**: Bottom bar → click encoding → "Save with Encoding" → choose correct option
- **Notepad++**: Menu → Encoding → select correct option

### Localisation .yml Specifics

- First line **must** be `l_english:` (or `l_simp_chinese:` for Chinese)
- Keys: ASCII only (A-Z, 0-9, `_`, `.`, `-`). No spaces, no Chinese characters in keys.
- Values: wrapped in double quotes, on one line. Use `\n` for line breaks.
- File name must end with `_l_<language>.yml` (e.g., `mymod_l_english.yml`)
- Override vanilla without replacing files: put overrides in `localisation/english/replace/`

## File Naming & Loading Order

- **Same filename = overwrite vanilla entirely.** The game replaces the whole file.
- **Different filename = add new content.** New files are loaded alongside vanilla.
- **Files load in ASCII order.** Prefix `00_` loads first, `zz_` loads last. Use this to control load order when your files depend on each other.
- **You CANNOT merge individual lines from vanilla.** To modify a vanilla file, copy the ENTIRE file, then edit. This means every game update may require re-merging your changes.

### Defines special rule
Never copy the entire `00_defines.lua`. Each patch changes it. Instead, create a new `.lua` file in `common/defines/` that overrides only the values you need:
```lua
NDefines.NGame.START_DATE = "1936.1.2.12"
```

## The Dependency Order

Create files in this order for any new feature. Each step defines IDs that later steps will reference.

```
1. Localization placeholders
   └─ All string keys that other files will reference
         │
2. GFX registration (interface/*.gfx)
   └─ Icon/spirit/sprite names referenced by ideas and focuses
         │
3. Ideas / National Spirits (common/ideas/)
   └─ Idea IDs referenced by focuses, events, decisions
         │
4. Characters (common/characters/)
   └─ Character IDs referenced by country history, events
         │
5. Focus tree (common/national_focus/)
   └─ References idea IDs, spirit IDs, event IDs
         │
6. Events (events/)
   └─ References idea IDs, character IDs, focus IDs
         │
7. Decisions (common/decisions/)
   └─ References idea IDs, event IDs
         │
8. Country history (history/countries/)
   └─ References all of the above
```

## Feature-by-Feature Guide

### Adding a Focus Tree
1. Define spirit icons → `interface/*.gfx`
2. Create national spirits → `common/ideas/` (use **hoi4-templates**: `民族精神模板.txt`)
3. Create the focus tree → `common/national_focus/` (use **hoi4-templates**: `国策模板重制.txt`)
4. Create triggered events → `events/`
5. Create all localization → `localisation/`

### Adding a Decision System
1. Define any new ideas the decisions grant → `common/ideas/`
2. Create the decision file → `common/decisions/` (use **hoi4-templates**: `两种决议模板和任务模板`)
3. Create triggered events if needed → `events/`
4. Create localization → `localisation/`

### Adding a New Country
1. Create country history → `history/countries/` (use **hoi4-templates**: `国家history文件模板`)
2. Register country tag via `common/country_tags/` and `common/countries/`
3. Create characters → `common/characters/` (use **hoi4-templates**: `人物模板.txt`)
4. Create national spirits if needed → `common/ideas/`
5. Create focus tree if applicable → `common/national_focus/`
6. Create starting units/OOB → `history/units/`
7. Create localization for country name + all IDs → `localisation/`

## Mod Setup Checklist

When creating a new mod from scratch:

- [ ] `descriptor.mod` — name, version, supported_version, tags, dependencies
- [ ] `.mod` file in mod root — identical name field to descriptor.mod
- [ ] Directory structure mirrors game directory: `common/`, `events/`, `history/`, `interface/`, `localisation/`, `gfx/`
- [ ] `localisation/` files exist with at minimum country name entries
- [ ] No files directly modify Hearts of Iron IV install directory — all in mod folder at `%USERPROFILE%/Documents/Paradox Interactive/Hearts of Iron IV/mod/`

## Verification Checklist

After writing files, verify before declaring done:

- [ ] All `icon = GFX_xxx` entries have corresponding `spriteType` in `interface/*.gfx`
- [ ] All idea/law/character IDs have localization entries (search for raw `key:0` in game)
- [ ] No two countries share the same tag
- [ ] Every `add_namespace = xxx` in events has corresponding `xxx.1.t` entry in localisation
- [ ] `descriptor.mod` supported_version matches target game version
- [ ] Launch game, check `error.log` — zero new errors from your mod
- [ ] Launch with `-debug` mode — many errors only appear in debug
- [ ] All `.yml` files start with `l_english:` (or `l_simp_chinese:`) on the first line

**REQUIRED BACKGROUND:** You MUST understand **hoi4-verification-before-completion** — use its checklist workflow for the verification step above.

## Debugging & Troubleshooting

### Error Interpretation 报错解读

**Three failure modes** — diagnose which one you're facing BEFORE anything else:

| Failure Mode | Signal | Diagnostic Path |
|---|---|---|
| **CTD** (crash) | Game exits instantly, no prompt | Check `setup.log` last-read file → binary search |
| **error.log hard error** | Log entry with file path + line number | Look up the error in the error dictionary (below) |
| **Silent failure** (worst) | Feature doesn't work, log is clean | Check the silent-failure checklist (see **hoi4-code-reference** → `references/常见报错解读.md`) |

**Reading an error.log entry** — the message contains YOUR file and line number:

```
[effect.cpp:402]: Invalid effect 'can_research' in common/national_focus/katarland.txt line : 2016
```

- `[effect.cpp:402]` — engine source location. The cpp name tells the error TYPE: `effect.cpp`=effect, `trigger.cpp`=trigger, `pdxscript.cpp`=syntax/variable, `texturehandler.cpp`=texture, `gui.cpp`=GUI, `eventmanager.cpp`=event. Not your code.
- `'can_research'` — the offending keyword. `can_research` is a TRIGGER misused as an effect.
- `common/national_focus/katarland.txt line : 2016` — your file and line. Go there first.

**Diagnostic decision tree** (simplified):

```
Game crashes?        → setup.log last entry → binary search
error.log entry?     → read file+line in entry → look up error dictionary → fix → clear log → retest
Clean log, no effect → silent-failure checklist (add_namespace / encoding / dependency order first)
```

**Complete error dictionary** (12 categories: unknown keywords, variables, scopes, parse errors, events, missing objects, localisation, GFX, GUI, focus/tech references, OOB, CTD + 12-item silent-failure checklist): read **hoi4-code-reference** → `references/常见报错解读.md`.

**Golden rules**:
1. The file path + line number in the error points to YOUR file — go there first
2. Always clear error.log before retesting, or old and new errors mix
3. Every error in `common/` folders should be fixed, never skipped
4. Test with `-debug` launch option — many errors only appear there

### Log File Locations

All logs are in `Documents/Paradox Interactive/Hearts of Iron IV/logs/`:

| Log File | Purpose | Usefulness |
|---|---|---|
| **error.log** | Non-fatal errors: broken syntax, invalid data | **Critical** — fix everything from your mod |
| **game.log** | Actions taken by countries during gameplay | **High** — trace what actually happened |
| **setup.log** | Loading progress — reveals which file caused a crash | **High** — last-read file = crash suspect |
| **memory.log** | Memory usage during loading | Medium — helps identify crash timing |
| **exceptions.log** | Stack trace on CTD | Low — often cryptic |
| **system_debug.log** | Interface errors | Medium |
| **text.log** | Localization key conflicts | Medium |

### Launch Options

Add in Steam (right-click HOI4 → Properties → Set Launch Options):

| Option | What it does | When to use |
|---|---|---|
| **`-debug`** | Full error logging, debug tooltips, Nudge! tool on title screen | **Always during development** |
| **`-crash_data_log`** | Logs last-read code line before crash to `crashes/` folder | Investigating CTD (slows game significantly) |

### Crash Causes by Timing

| When | Common Causes | Fix |
|---|---|---|
| **During Loading** | Complete overwrite of `common/national_focus/` without generic files; broken map BMP files; invalid province IDs in victory points | Port generic files into mod; check BMP format/dimensions; validate province IDs |
| **Country Selection** | Country has no valid capital; naval OOB uses pre-1.12 airwing format; AI template mismatch | Add `capital = <state_id>` in history file; update OOB format; check AI templates |
| **Mid-Game** | AI crash from missing AI templates or unowned states; `map/buildings.txt` invalid building positions | Run Nudge! → Find Error → Validate in State; assign owners to all states |

### Binary Search Debugging

When the error log isn't helpful:
1. **Clean error.log** before testing
2. **Remove half** your mod's files/folders
3. If crash disappears → problem is in removed half. If not → problem is in remaining half.
4. **Repeat** halving until you find the problematic file
5. Do the same with `replace_path` entries — remove all, add back one at a time

### Nudge! Tool (Building Crashes)

Any mod changing states, provinces, or buildings can cause crashes because 3D model positions become stale:
1. Launch with `-debug`, click "Nudge!" on title screen
2. Go to **Buildings** tab
3. Click **Find Error** → **Validate in State** (repeat until clean)
4. **Save** — outputs to `Documents/.../map/buildings.txt`
5. **Manually copy** the saved file to your mod's `map/` folder

### Useful Console Commands

| Command | Purpose |
|---|---|
| `tdebug` | Show state ID and province ID on hover |
| `reload focus` | Reload focus trees (crashes on error) |
| `reloadtechnologies` | Reload tech files |
| `reload localization` | Reload localisation files |
| `reloadfx all` | Reload visual effects |
| `event <id>` | Fire a specific event |
| `event <id> <TAG>` | Fire event for a specific country |
| `tag <TAG>` | Switch to another country |
| `observe` | Watch AI play (type `tag <TAG>` to return) |
| `aiview` | Show AI priorities and weights |
| `Focus.AutoComplete` | Instant focus completion |
| `Focus.NoChecks` | Remove focus trigger checks |
| `nocb` | Remove diplomatic action restrictions |
| `tacc` | Accept all diplomatic requests |
| `debug` | Show debug info (country tags, province borders) |

## Recommended Tools

| Tool | Type | Purpose |
|---|---|---|
| **VS Code + CWTools** | Editor + Extension | Syntax validation, linting, error catching for Paradox script. Catches coding/localization errors before you launch the game. |
| **HOI4 Mod Utilities** | VS Code Extension | Preview focus trees and technology layouts without launching the game. |
| **Irony Mod Manager** | Standalone | Alternative mod launcher with deterministic load order, conflict detection, and conflict resolution. |
| **WinMerge** | Standalone | File comparison tool — essential for merging your mod files after a game update changes vanilla files. |
| **GIMP / Photoshop** | Image Editor | Required for map bitmap editing. Paint.net and MS Paint break indexed BMP files. |

## Common Mistakes

| Mistake | Reality |
|---|---|
| Creating focus tree before spirit IDs exist | Define spirits first, then reference their IDs in focuses — otherwise silent failure |
| Writing events without `add_namespace` | Events silently won't fire. Always declare namespace first. |
| Forgetting GFX registration for icons | `icon = GFX_xxx` without matching `spriteType` → blank icon in game |
| Working in game install directory | Always work in mod folder. Game updates wipe install-directory changes. |
| Missing `localisation/` folder | Game shows raw keys like `mod_country.1.t` instead of readable text |
| Duplicate country tags | Two countries with same tag → second silently overwrites first in game |
| Backslashes in paths | `path="mod\mymod"` → `\m` is an escape character. Always use forward slashes: `"mod/mymod"` |
| Copying entire `00_defines.lua` | Game patches change this file. Override individual values in a separate file. |
| Non-UTF-8 encoding on .txt files | Game silently skips or misreads the file. Always UTF-8 without BOM. |
| Missing `l_english:` on .yml first line | Game cannot parse ANY strings in that file. |

## Red Flags — Stop and Recheck

- Creating a file that references an ID you haven't defined yet in an earlier step
- You skipped GFX registration
- You skipped localization entirely
- Localization shows raw keys (`mod_country.1.t`) in game
- You are editing files in the game install directory instead of the mod folder
- `error.log` has new entries after enabling your mod
- You didn't test with `-debug` launch option
- Paths use backslashes instead of forward slashes
- A `.yml` file doesn't start with `l_english:` on line 1
- You're about to copy the entire `00_defines.lua` instead of overriding individual values

**All of these mean: Stop. Go back to the dependency order and verify each step.**

## Cross-Reference

- **hoi4-code-reference**: Effect/trigger/modifier syntax for everything you write
- **hoi4-templates**: Complete file skeletons — use one per file you create
- **hoi4-version-migration**: If porting an existing mod, check what changed between versions
- **hoi4-gui-shader**: Advanced GUI scripting and shader code

## Reference Materials

In-depth tutorials for specific file types (PDF/DOCX — for human reference):
- `本地资料库/如何制作一个MOD(初级篇).pdf`
- `本地资料库/如何制作一个MOD(高级篇).pdf`
- `本地资料库/基础代码\代码教程合集\` — 51 files covering events, decisions, focuses, characters, OOB, and more
