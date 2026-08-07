---
name: hoi4-map-making
description: Use when 修改 HOI4 钢4 地图——省份（province）、州（state）、战略区（strategic region）、铁路与补给（supply）、邻接（adjacency）、地图渲染、Nudge 工具、provinces.bmp/definition.csv 格式、国家颜色 RGB 不一致问题。Also use when 新建地块、调整建筑位置或遇到地图相关报错需要对照地图文件格式。
---

# HOI4 地图制作（Map Making）

## Overview

**管什么**：HOI4 地图制作的"哪里"——地图文件格式、省份颜色系统、新建省/州流程、战略区、补给铁路、渲染修改与 Nudge 工具用法。

**何时用**：
- 要修改地图（新省份、新州、战略区调整、铁路补给、邻接设定）
- 要处理地图渲染、国家颜色 RGB 不一致问题
- 要用 Nudge 工具（建筑位置、地块数据、新建地块）
- 遇到地图相关报错需要对照地图文件格式（provinces.bmp ↔ definition.csv ↔ 战略区 ↔ 州 ↔ 建筑）

**何时不用**：
- 地图报错的**报错含义解读** → **hoi4-debugging**（州/地图类字典）
- 建筑崩溃的 Nudge 修复流程 → **hoi4-debugging**
- 新国家/新州的 history 文件 → **hoi4-mod-workflow** / **hoi4-templates**

**⚠️ 铁律**：地图文件彼此强关联（provinces.bmp ↔ definition.csv ↔ 战略区 ↔ 州 ↔ 建筑），**改一处必须联动改多处**，否则出现一堆地图报错。**修改前务必备份地图。**

## Quick Reference

### 核心文件与作用

| 文件 | 位置 | 作用 |
|---|---|---|
| `provinces.bmp` | `map/` | 省份底图：每个省份用**唯一 RGB 颜色**填充 |
| `definition.csv` | `map/` | 把 RGB 颜色映射到省份 ID，并定义类型/沿海/地形 |
| `adjacencies.csv` | `map/` | 特殊连接（岛屿间虚线、不可通行地形的通道） |
| `adjacency_rules.txt` | `map/` | 邻接规则 |
| `strategicregions/*.txt` | `map/` | 战略区：省份归属、海省地形、天气、建筑放置 |
| `states/*.txt` | `history/states/` | 州：省份组合、人力、state_category、建筑 |
| `supply_nodes.txt` / `rocketsites.txt` | `map/` | 补给节点 / 火箭发射场 |
| `constants.fxh` | `map/` 渲染 | 地图渲染更改说明模板（水/地形/色调整改） |

### definition.csv 格式

每行一个省份，逗号分隔 6 个字段：

```
<省份ID>,<R>;<G>;<B>,<type>,<coastal>,<terrain>
200,0;0;0,land,no,plains
201,128;0;0,sea,yes,ocean
```

- 省份 ID 全局唯一；RGB 颜色全局唯一（与 provinces.bmp 一致）
- **definition.csv 缺行会导致后续颜色-ID 映射全部错位**（地形模式破碎也是此症状）

### 国家颜色 RGB 不一致问题

- 决定国家领土颜色的文件：`common/countries/colors.txt` 或 `cosmetic.txt`，参数 `color`（可用 rgb 或 hsv）
- **原因**：游戏在读取 RGB 值基础上给饱和度/明度乘系数，导致实际显示颜色与文件值不一致
- **解法 1（改 defines）**：在 `common/defines/` 建一个 `*.lua`，将系数设为 1，使文件 RGB 与游戏显示完全一致
- **解法 2（反算）**：按公式反推"文件中应设的 RGB"（文件值 = 期望显示值 ÷ 系数）；一般公式与具体系数见 `references/地图系列/HOI4关于国家在游戏文件中的RGB值与实际在游戏中的不一致的解决方法.md`

### 地图修改流程（按规模选入口）

| 需求 | 读哪个 |
|---|---|
| 完整地图制作 | `references/地图制作完整教程（wiki译文）.md`（wiki 完整格式） |
| 快速上手 | `references/08-地图制作.md`、`references/地图制作1.0.md` |
| 深改渲染/光影 | `references/地图系列/地图渲染教程/如何进行地图修改（2021完全版）.md` + `references/地图系列/地图渲染教程/constants（地图渲染更改说明模板）.fxh` |
| 国家颜色 | `references/地图系列/HOI4关于国家在游戏文件中的RGB值与实际在游戏中的不一致的解决方法.md` |
| 战略区 | `references/战略区教程（wiki译文）.md` |
| 州（state） | `references/省份State教程（wiki译文）.md` |
| 补给与铁路 | `references/补给与铁路教程（wiki译文）.md` |
| 地图工具 | `references/地图格式_导读.md`、`references/补给与铁路_导读.md`（导读导航） |

### Nudge 工具（游戏内地图编辑）

B站 Nudge 教程（分 P 对应功能）：
- **P2 战略区**（`references/B站Nudge教程-P2战略区.md`）
- **P3 铁路和补给**（`references/B站Nudge教程-P3铁路和补给.md`）
- **P4 邻接设定**（`references/B站Nudge教程-P4邻接设定.md`）
- **P5 地块数据**（`references/B站Nudge教程-P5地块数据.md`）
- **P6 新建地块**（`references/B站Nudge教程-P6新建地块.md`）
- 完整工具说明：`references/Nudge地图工具教程（wiki译文）.md`

**Nudge 关键用途**：调整建筑位置（`map/buildings.txt` 报错时用 Nudge 重算/重分配，见 hoi4-debugging）；**Nudge 修改后需同步位图**（provinces.bmp 等）。

### 地图修改通用流程（简版）

1. **备份**地图文件（铁律）
2. 改 `provinces.bmp`（唯一 RGB 颜色）→ 同步改 `definition.csv`（ID/颜色/类型/沿海/地形）
3. 更新战略区归属（`strategicregions/*.txt`）与州归属（`history/states/*.txt`）
4. 检查邻接（`adjacencies.csv` / `adjacency_rules.txt`）
5. 检查补给节点/火箭发射场（`supply_nodes.txt` / `rocketsites.txt`）
6. -debug 启动验证（地图报错见 hoi4-debugging 州/地图类字典）

## 常见错误表

| 现象 | 原因 | 修复 |
|---|---|---|
| `Bitmap and province definition disagree ... coastal` 或地形模式破碎 | provinces.bmp 与 definition.csv 颜色不一致，或 definition.csv 缺行导致颜色-ID 映射错位 | 逐行核对 definition.csv 与 provinces.bmp 的颜色映射 |
| `State #743 does not have a category defined` | 新州缺 `state_category`/`manpower` | 补州定义（对照原版州文件格式） |
| 战略区/补给报错或省份消失 | 战略区 ID 重复或省份重复归属 | 检查 `strategicregions/*.txt` 中省份 ID 唯一性 |
| 铁路补给不连通 | 补给节点引用不存在的 state | 核对 `supply_nodes.txt` 中 state ID 存在 |
| Nudge 调整后游戏内没变化 | 修改后未同步位图文件 | 保存后同步 provinces.bmp 等位图 |
| 岛屿/海峡无法通行 | 邻接遗漏 | 补 `adjacencies.csv` 条目（含不可通行地形通道） |
| 新建省后原邻接错乱 | 地图横向循环，边界处对角连接 | 检查 `Map invalid X crossing` 报错处的四省共角像素 |

## Reference Files

| 文件 | 内容 | 何时读 |
|---|---|---|
| `references/08-地图制作.md` | 核心文件与作用表、definition.csv 格式、新建省流程与常见错误 | 地图制作总入口 |
| `references/地图制作1.0.md` | 地图制作入门 | 快速上手 |
| `references/地图制作完整教程（wiki译文）.md` | wiki 完整格式（深改必备） | 完整地图制作 |
| `references/地图系列/地图渲染教程/如何进行地图修改（2021完全版）.md` | 渲染深改流程 | 渲染/光影修改 |
| `references/地图系列/地图渲染教程/constants（地图渲染更改说明模板）.fxh` | 渲染更改说明模板 | 渲染参数修改 |
| `references/地图系列/HOI4关于国家在游戏文件中的RGB值与实际在游戏中的不一致的解决方法.md` | 国家颜色 RGB 不一致的解法与公式 | 国家颜色不对时 |
| `references/地图系列/完美MOD教室-地图篇.md` | 地图篇教程 | 综合参考 |
| `references/战略区教程（wiki译文）.md` | 战略区格式 | 做战略区时 |
| `references/省份State教程（wiki译文）.md` | 州格式 | 做州时 |
| `references/补给与铁路教程（wiki译文）.md` | 补给与铁路 | 做补给铁路时 |
| `references/Nudge地图工具教程（wiki译文）.md` + B站Nudge教程 P2-P6（5 个） | Nudge 各功能 | 用 Nudge 时 |
| `references/地图格式_导读.md`、`references/补给与铁路_导读.md` | 主题导读导航 | 快速定位 |

## Cross-Reference

**依赖**：
- **hoi4-debugging** → 州/地图类报错字典、Nudge 修复建筑崩溃、LastRead 对照表（provinces.bmp 错误）
- **hoi4-mod-workflow** → 文件放置、编码规则、依赖链
- **hoi4-code-reference** → 相关 effect/trigger

**被引用**：
- **hoi4-modding** → 路由表"地图"场景
- **hoi4-brainstorming** → Q4 系统能力菜单（地图改动能力）
- **hoi4-verification-before-completion** → playtest 清单中地图改动验证

## Process Interface

任务超出单点查询（大范围地图改动、涉及渲染与多文件联动）时，先走 **hoi4-brainstorming** → **hoi4-writing-plans** 流程链，本 skill 只做地图知识支撑。
