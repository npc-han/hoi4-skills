---
name: hoi4-templates
description: Use when creating new HOI4 mod files from scratch — focus trees, events, decisions, national spirits, characters, advisors, laws, sub-doctrines, GFX registration, scripted GUIs, custom diplomacy, equipment, units, buildings, or bookmarks. Use when you need a complete working file skeleton to fill in with custom values
---

# HOI4 Templates

## Overview

Ready-to-use file templates for every HOI4 mod file type. Each template is a complete, working skeleton covering all fields and sub-structures. Copy the skeleton, replace placeholder values, and connect to localization.

## Template Catalog

### Politics & Ideas

| File type | Template | Game path |
|---|---|---|
| Focus tree | `templates/国策模板重制.txt` | `common/national_focus/` |
| National spirits | `templates/民族精神模板.txt` | `common/ideas/` |
| Laws & ideas | `templates/法案模板.txt` | `common/ideas/` |
| Sub-doctrines | `templates/子学说模板.txt` | `common/ideas/` |
| Power balance | `templates/权力平衡教学.txt` | `common/power_balance/` |
| Inner circle | `templates/核心圈模板.txt` | `common/characters/` |
| Faction system (1.17) | `templates/1.17阵营模板 /` | `common/factions/` |
| TFR economy system | `templates/LRD-TFR经济体制修改模版-by康米斯特/` | Multiple paths |
| Inner circle (full mod) | `templates/预制核心圈内容（含文档和用例）/` | Multiple paths |

### Events & Decisions

| File type | Template |
|---|---|
| Country events & news | `templates/事件及新闻模板.txt` |
| Decisions & missions | `templates/两种决议模板和任务模板（重置） .txt` |
| Ethiopia industrial plot | `templates/埃塞工业地块决议模板.txt` |
| Scripted triggers | `templates/脚本条件_scripted_triggers.txt` |
| Restrict diplomacy button | `templates/【限制外交按钮使用】自定义你的文件名_scripted_triggers.txt` |

### Characters & Military

| File type | Template | Game path |
|---|---|---|
| Characters | `templates/人物模板.txt` | `common/characters/` |
| Advisors / cabinet | `templates/内阁制作模板+原版特质列表.txt` | `common/ideas/` |
| Military ministers | `templates/军事、政府内阁全部特性代码与模板.txt` | `common/ideas/` |
| Character traits | `templates/特质制作模板+修正列表+装备种类及装备修正.txt` | `common/ideas/` |
| Army history entries | `templates/军队历史词条模板.txt` | `history/units/` |
| Army units | `templates/单位模板（陆军）.txt` | `common/units/` |
| Equipment | `templates/装备模板（陆军）.txt` | `common/units/equipment/` |
| Equipment design | `templates/装备设计模板（坦克，飞机，舰船）.txt` | `common/units/equipment/` |
| Equipment modules | `templates/装备模块（equipment_modules）教程（Phoebe制）.txt` | `common/units/equipment/modules/` |
| Tactics | `templates/战术模板.txt` | `common/combat_tactics.txt` |

### Country Setup

| File type | Template | Game path |
|---|---|---|
| Country history | `templates/国家history文件模板【胡桃翻新版本】.txt` | `history/countries/` |
| Starting units/lines | `templates/开局部队和生产线设置（完全版）.txt` | `history/units/` |
| Bookmarks | `templates/Bookmarks模板.txt` | `common/bookmarks/` |
| Opinion modifiers | `templates/国家外交关系修正代码_opinion_modifiers.txt` | `common/opinion_modifiers/` |
| Custom diplomacy | `templates/自定义外交行动模板.txt` | `common/diplomatic_actions/` |
| Intelligence agencies | `templates/情报机构_intelligence_agencies.txt` | `common/intelligence_agencies/` |
| Spy system | `templates/间谍代码（波罗）.txt` | `common/intelligence_agency/` |

### Graphics & UI

| File type | Template | Game path |
|---|---|---|
| GFX registration | `templates/常用gfx文件注册模板.txt` | `interface/*.gfx` |
| Scripted localization | `templates/脚本本地化(Scripted_loc)模板by秋起.txt` | `common/scripted_loc/` |
| Custom manufacturers | `templates/自定义制造商（七次修改版...）.txt` | `common/ideas/` |

### Mod Setup & Localisation

| File type | Template | Game path |
|---|---|---|
| Mod descriptor | *(see **hoi4-mod-workflow** for full .mod/descriptor.mod format)* | Mod root |
| Country tags | *(see **hoi4-mod-workflow** for country_tag registration format)* | `common/country_tags/` |
| On actions | *(see built-in mods: 预制核心圈内容 for on_actions example)* | `common/on_actions/` |
| Localisation (CN) | `templates/脚本本地化(Scripted_loc)模板by秋起.txt` | `localisation/simp_chinese/` |
| Defines override | *(Create new .lua file, override only needed values — NEVER copy 00_defines.lua)* | `common/defines/` |

**Encoding note for localisation**: `.yml` files MUST use UTF-8 with BOM. First line MUST be `l_simp_chinese:` (for Chinese). Keys: ASCII only. Values in double quotes, one line. File name: `xxx_l_simp_chinese.yml`.

| File type | Template |
|---|---|
| Province setup | `templates/省份模板.txt` |
| New map mode | `templates/新建地图模式模板&备忘录.txt` |

### Other Systems

| File type | Template |
|---|---|
| Buildings | `templates/建筑模板最新版.txt` |
| Sub-ideologies | `templates/原版子意识形态制作方法.docx` |
| AI strategy | `templates/AI战略编写模板扩充版 原作者：希儿（憨批）.txt` |
| AI areas | `templates/[菜鸟模组教程]ai_areas.txt` |
| Technology | `templates/科技教程（作者：秋起. Antarctica）.txt` |
| Special projects | `templates/sp_project教程（整合版v1）.txt` |
| Focus + spirits (tutorial) | `templates/第五课-国策与民族精神.docx` |
| Quick reference (effects+templates) | `templates/提词器 - 常用模板.txt` |

## How to Use

1. **Locate** your file type in the catalog above
2. **Read** the corresponding template file in `templates/`
3. **Copy** the skeleton into your mod directory at the listed game path
4. **Replace** placeholder IDs (TAG, my_idea, my_focus) with your own
5. **Create** matching localization entries in `localisation/` for every new string key

## Common Mistakes

| Mistake | Fix |
|---|---|
| Leaving placeholder TAG in template | Replace all `TAG` with your 3-letter country tag |
| Using template IDs without localization | Every `my_idea` needs a `my_idea:0 "Name"` entry in localisation |
| Copying only part of template | Templates have nested blocks — ensure outermost structure is complete |
| Forgetting GFX registration | If template uses `icon = GFX_xxx`, also create `interface/*.gfx` registration |

## Cross-Reference

- **hoi4-code-reference**: Look up effect/trigger/modifier syntax used within templates
- **hoi4-mod-workflow**: The correct order to create files — define before you reference
- **hoi4-gui-shader**: For advanced scripted GUI and shader templates beyond basic GFX registration
