---
name: hoi4-advanced-systems
description: Use when 制作 HOI4 钢4 进阶系统——特殊项目（sp_project）、阵营系统 1.17、核心圈、MIO 军工组织、权力平衡、子学说、AI 战略、脚本条件（scripted triggers）/脚本本地化（scripted loc）。Also use when 需要这些系统的代码模板、字段说明或版本要求（1.14/1.16/1.17）。
---

# HOI4 进阶系统（Advanced Systems）

## Overview

**管什么**：HOI4 的高阶系统代码——特殊项目、阵营、核心圈、MIO、权力平衡、子学说、AI 战略、脚本条件/脚本本地化。这些系统各有专属文件路径与语法，不属于普通国策/事件/决议范畴。

**何时用**：
- 要给 mod 加特殊项目、阵营系统、核心圈、MIO、权力平衡、子学说
- 需要写 AI 战略（`ai_strategy` / `add_ai_strategy`）
- 需要脚本条件（`scripted_trigger`）或脚本本地化（`scripted_loc`）
- 不确定这些系统的版本要求（MIO=1.14、核心圈=1.16、阵营重做=1.17）

**何时不用**：
- 普通国策/事件/决议/人物模板 → **hoi4-templates**
- effect/trigger/scope 语法查询 → **hoi4-code-reference**
- 版本间语法迁移 → **hoi4-version-migration**

**版本锚定（1.17.x）**：以下系统有明确版本门槛——MIO 需 1.14+、核心圈需 1.16+、阵营重做需 1.17+。设计时先确认目标版本，混用旧语法会静默失败。

## Quick Reference

### 特殊项目（sp_project，1.13+）

- **构成四要素**：项目定义 `special_project_id`、专业化 `special_project_specialization`（所属领域：航空/核技术等）、科学家 `scientist`、设施 `special_project_facility`
- **文件路径**：`common/special_projects/`、`common/special_project_specializations/`、`common/scientists/`、`common/special_project_facilities/`
- **研究周期三阶段**：启动阶段（Establishing，取消后重头再来）→ 初始研究阶段（Initial research，取消后记住已完成）→ 原型阶段（Prototyping，多次迭代按 Complexity 累计进度到 100%，取消后记住进度）
- **必填字段**：`specialization = "token"`（所属领域，必须指定）；奖励在项目完成后发放（`on_completion` 等）
- 完整结构见 `references/sp_project教程（整合版v1）.txt`

### 阵营系统 1.17（Faction Rework）

- **文件路径**：`common/faction_goals/`（goals）、`common/faction_rules/`（rules）、`common/faction_templates/`（templates）、`common/faction_member_upgrades/`
- **核心概念**：faction goals（阵营目标，决定 AI 阵营走向）→ faction rules（成员规则）→ templates（模板）
- **注意**：1.17 阵营重做改变了旧版阵营脚本结构；教程与完整模板见 `references/阵营系统1.17教程（wiki译文）.md`；**完整可用工程模板在 hoi4-templates 的 `templates/1.17阵营模板/`**

### 核心圈（Focus Inlay Window，1.16+）

- **文件路径**：`common/focus_inlay_windows/<TAG>_inner_circle_inlay_window.txt` + `interface/<TAG>_inner_circle_scripted_gui.gui`
- **核心字段**：`window_name`（窗口名）、`internal = yes`（在国策内）、`visible = {...}`（显示条件）、`scripted_images = {...}`（脚本化图像，如元首肖像按国家领导人切换）
- **要点**：`<TAG>` 替换为国家 tag；脚本化 GUI 接线在 interface .gui 文件
- 模板见 `references/核心圈模板.txt`

### MIO 军工组织（1.14+）

- **文件路径**：`common/army_intel_offices/`（MIO 定义）、`common/military_industrial_organizations/`（1.14 正式 MIO）、`common/characters/`（MIO 领导人）
- **要点**：MIO 有专属分支树（branches）与升级，引用不存在的分支会报错；MIO 领导人需在 characters 中定义
- 完整教程见 `references/MIO军工组织教程（wiki译文）.md`

### 权力平衡（Balance of Power）

- **文件路径**：`common/bop/`
- **核心字段**：`initial_value`（初始位置，-1~1）、`left_side`/`right_side`（两侧名称）、`decision_category`（绑定决议类别）、`range`（中间点范围 + `side` 判定点范围，各带 modifier）
- **判定点**：每个 `range` 块是一个判定点，`min`/`max` 定区间，`modifier` 是区间内生效的效果；一侧可写多个判定点
- 模板见 `references/权力平衡教学.txt`

### 子学说（Sub-doctrines）

- **文件路径**：`common/doctrines/subdoctrines/<branch>/`（如 land）
- **核心字段**：`track`（学说类型）、`name`/`description`、`icon = GFX_...`（需注册）、`xp_cost`/`xp_type`、`available = {...}`（可用条件）、`visible = {...}`、`ai_will_do = { base + modifier }`（AI 选择权重）
- 模板见 `references/子学说模板.txt`

### AI 战略（ai_strategy）

- **添加方式**：在国策/事件/决议中 `add_ai_strategy = { type = ... id = ... value = ... }`
- **战略块结构**：`enable = {...}`（开启条件，可重复触发）、`abort = {...}`（终止条件）、`abort_when_not_enabled = yes`（未满足启用条件时终止）
- **`ai_strategy` 块**：`type = 战略种类`、`id = 战略要执行的种类（装备/兵种/模板等）`
- **注意**：`value` 有些种类需要双引号（如结盟战略的对象国家代码）；数值不生效常是静默失败——先查 enable 条件
- 完整模板见 `references/AI战略编写模板扩充版.txt`

### 脚本条件 / 脚本本地化

- **脚本条件**：`scripted_trigger` 定义于 `common/scripted_triggers/`，可在 trigger 位置复用；模板见 `references/脚本条件_scripted_triggers.txt`
- **脚本本地化**：`scripted_loc` 定义于 `common/scripted_localisation/`，动态生成本地化文本；模板见 `references/脚本本地化(Scripted_loc)模板.txt`
- 语法细节路由 **hoi4-code-reference**

## 常见错误表

| 现象 | 原因 | 修复 |
|---|---|---|
| MIO 报错引用不存在的内容 | 引用了不存在的 MIO 分支/升级线 ID | 对照教程与已建 MIO 定义核对 ID 拼写 |
| 核心圈 UI 不显示或显示错误窗口 | 字符 ID 与 `common/characters/` 重复，或 `<TAG>` 未替换 | 检查 inlay window 与 .gui 的 TAG 一致性与唯一性 |
| 特殊项目无法启动/进度异常 | 升级线括号不闭合或 `specialization` 未指定 | 检查特殊项目结构（启动/初始/原型三阶段字段） |
| 阵营 goal 报 `unknown keyword` | 阵营 1.17 语法错误（如用了旧版阵营语法） | 对照 1.17 教程与 hoi4-templates 的 1.17 阵营模板 |
| AI 战略数值不生效（无报错） | `enable` 条件不满足，或 `value` 需要引号没加 | 检查 enable 条件；结盟类战略对象国家代码加双引号 |
| 子学说在游戏里不可见 | `visible`/`available` 条件不满足（如缺 `has_dlc`），或 `icon = GFX_...` 未注册 | 检查条件与 GFX 注册（interface/*.gfx，大小写敏感） |

## Reference Files

| 文件 | 内容 | 何时读 |
|---|---|---|
| `references/sp_project教程（整合版v1）.txt` | 特殊项目完整教程（四要素/三阶段/自定义教程） | 新建特殊项目时 |
| `references/阵营系统1.17教程（wiki译文）.md` | 阵营重做 1.17 教程 | 做阵营系统时 |
| `references/核心圈模板.txt` | 核心圈 inlay window 模板（含 scripted_gui 位置） | 做核心圈时 |
| `references/MIO军工组织教程（wiki译文）.md` | MIO 完整教程 | 做 MIO 时 |
| `references/权力平衡教学.txt` | 权力平衡模板（common/bop） | 做权力平衡时 |
| `references/子学说模板.txt` | 子学说模板（含 ai_will_do） | 做子学说时 |
| `references/AI战略编写模板扩充版.txt` | AI 战略模板（add_ai_strategy/ai_strategy 结构） | 给国策/事件加 AI 战略时 |
| `references/脚本条件_scripted_triggers.txt` | scripted_trigger 模板 | 写脚本条件时 |
| `references/脚本本地化(Scripted_loc)模板.txt` | scripted_loc 模板 | 写脚本本地化时 |

## Cross-Reference

**依赖**：
- **hoi4-code-reference** → effect/trigger/scope 语法、modifier 表
- **hoi4-templates** → `templates/1.17阵营模板/`（完整可用工程）、各系统模板
- **hoi4-version-migration** → 各系统版本门槛（MIO=1.14/核心圈=1.16/阵营=1.17）
- **hoi4-mod-workflow** → 文件放置、编码规则

**被引用**：
- **hoi4-modding** → 路由表"进阶系统"场景
- **hoi4-brainstorming** → Q4 系统能力菜单（判断可做什么系统）
- **hoi4-debugging** → 排查进阶系统报错时对照

## Process Interface

任务超出单点查询（要新增一个完整系统、涉及多文件改动）时，先走 **hoi4-brainstorming** → **hoi4-writing-plans** 流程链，本 skill 只做系统知识支撑。
