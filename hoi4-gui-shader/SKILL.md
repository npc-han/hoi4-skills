---
name: hoi4-gui-shader
description: Use when creating custom GUI windows (container windows, buttons, icons, text boxes, gridboxes), building super-event UI templates, writing HLSL shaders for HOI4 visual effects, working with variable-driven dynamic UI, or implementing Collection/array-based UI lists. Also use when regular interface modding is insufficient and programmatic UI with game-state-driven behavior is needed
---

# HOI4 GUI and Shader

## Overview

HOI4's interface system goes beyond basic `.gfx` file edits. Scripted GUIs enable programmatic windows driven by game state variables. The shader system (HLSL) enables custom visual effects.

**Prerequisites:** Must be comfortable with basic modding (focus trees, events, ideas) before attempting scripted GUIs. Shader work requires basic HLSL knowledge.

## When to Use vs When NOT to Use

**Use this skill when:**
- Creating a custom window that vanilla GUI can't express
- Dynamic UI elements controlled by in-game variables
- Building a super-event UI template (nuke screen, major war declaration screen)
- Custom HLSL visual effects
- Data-driven lists using Collection/array + gridbox

**Do NOT use for:** Basic GFX icon registration or simple interface color changes — use **hoi4-templates** instead.

## Quick Reference: GUI Components

### Common Element Types (in `containerWindowType`)

| Element | Use |
|---|---|
| `buttonType` | Clickable buttons with effect callbacks |
| `iconType` | Static image/sprite display |
| `instantTextboxType` | Text display with font, size, color control |
| `gridboxType` | Data-driven grid layouts (requires Collection/array) |
| `listboxType` | Scrollable item lists |
| `editBoxType` | Text input fields |
| `progressbarType` | Fill bars (use with variables) |

### Template Files
- Full GUI structure: `本地资料库/高级代码\GUI\GUI模板（临时版）.txt`
- Scripted GUI file format: `本地资料库/高级代码\GUI\GUI制作模板（文件类型）/名称_scripted_gui.txt`
- Complete super-event project: `本地资料库/高级代码\GUI\超事件模板/` (common/ + events/ + gfx/ + interface/ + localisation/ + music/)

## Quick Reference: Shader (HLSL)

HOI4 uses HLSL pixel shaders. Key resources:
- `本地资料库/高级代码\Shader\HLSL着色器教程-基础语法篇.pdf` — Syntax basics
- `本地资料库/高级代码\Shader\HLSL着色器头文件(.fxh)教程.pdf` — Header file patterns
- `本地资料库/高级代码\Shader\案例\秋起图书馆赠送着色器.shader` — Reference implementation
- `本地资料库/高级代码\Shader\案例\曲线Shader使用教程/` — Curve/line graph shader tutorial with full project files

## Quick Reference: Variables, Collections, Arrays

For driving dynamic GUI:
- `本地资料库/高级代码\变量、Collection、数组和gridbox代码教程\基础变量.txt` — Variable operations
- `本地资料库/高级代码\变量、Collection、数组和gridbox代码教程\Collection教程-修订2.txt` — Collection creation and iteration
- `本地资料库/高级代码\变量、Collection、数组和gridbox代码教程\pum数组、循环教程.txt` — Array and for-loop patterns
- `本地资料库/高级代码\变量、Collection、数组和gridbox代码教程\动态修正效果显示的自动化解决方案.pdf` — Dynamic modifier display in GUI
- `本地资料库/高级代码\变量、Collection、数组和gridbox代码教程\event_target讲解.txt` — Event target scoping for GUI callbacks

## Implementation Pattern

1. **Design** — sketch layout with position/size/dimensions
2. **Register GFX assets** — all icons/sprites used in the GUI via `interface/*.gfx`
3. **Write scripted GUI** — `containerWindowType` with child elements (use template above)
4. **Add callbacks** — connect buttons to events/effects via `onclick` handlers
5. **Wire variables** — if dynamic, connect display elements to game variables
6. **Create localization** — all visible text strings
7. **Test** — trigger the GUI in-game, verify rendering, check `error.log`

## Common Mistakes

| Mistake | Reality |
|---|---|
| Missing `Orientation` on container | Defaults to UPPER_LEFT, breaking all position calculations — always set explicitly |
| `spriteType` without GFX registration | Silent fail — image won't display. Always register in `interface/*.gfx` first. |
| Array index without bounds check | HOI4 doesn't bounds-check Collection access — can cause CTD. Always guard with `if` check. |
| Shader uses wrong HLSL feature level | HOI4 expects SM 3.0 compatible HLSL — newer features won't compile |
| Forgetting `name` field on elements | Unnamed elements can't be referenced by scripted effects. Name every interactive element. |

## Cross-Reference

- **hoi4-code-reference**: Variable operation syntax, scope transitions used in GUI callbacks
- **hoi4-templates**: Basic GFX registration templates and scripted localization templates
- **hoi4-mod-workflow**: General dependency order — GUI files are created after ideas/events they reference
