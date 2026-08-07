---
name: hoi4-writing-plans
description: Use when 设计已批准或有 spec、要写 HOI4 钢4 实施计划——多文件改动（国策树/事件/决议/本地化联动）、需要文件依赖链排序、最小可加载单元步骤、每步验证点、BOM/编码检查、无占位符计划。Also use when 一个功能涉及多个 mod 文件需要先排执行顺序。
---

# HOI4 实施计划（Writing Plans）

## Overview

**管什么**：把已批准的设计转成**可直接执行**的 HOI4 mod 实施计划——文件清单与依赖链排序、最小可加载单元粒度、每步内嵌验证点、编码规则内嵌短表、无占位符纪律。

**何时用**：
- 设计已批准 / 有 spec，功能涉及**多个 mod 文件**（国策+事件+决议+本地化+GFX）
- 需要先排"先做哪个文件、后做哪个文件"的执行顺序
- 需要一个能逐步验证（`-debug` 进主菜单 / CWTools 校验）的计划

**何时不用**：
- 设计还没定 → 先走 **hoi4-brainstorming**（本 skill 的入口）
- 单文件小改动（一个国策改数值）→ 口头确认即可，不用写计划
- 要执行计划 → 交给 **hoi4-executing-plans**

## 入口

来自 **hoi4-brainstorming** 的用户批准——那是本 skill 的唯一入口。没有批准过的设计，不写计划。

## 四大检查点（写入每个计划）

| 检查点 | 落实位置 |
|---|---|
| **版本锚定** 1.17.x | 计划头"目标版本"；不用 1.14/1.16 旧语法 |
| **文件依赖链** | File Structure 依赖链序号排序 |
| **编码/BOM** | 编码规范内嵌短表 |
| **最小可加载验证** | 步骤粒度 + 每步验证点 |

## 编码规范内嵌短表（每个计划都要有）

| 文件类型 | 编码 | 首行 |
|---|---|---|
| `.yml`（本地化） | **UTF-8 带 BOM** | `l_simp_chinese:`（简体中文） |
| `.txt`（代码文件） | UTF-8 **无** BOM | — |
| `descriptor.mod` | UTF-8 **无** BOM（与 yml 相反！） | — |
| `.mod`（根目录） | 带 BOM | — |
| `.lua`（defines） | UTF-8 | 注释用 `--` 不是 `#` |

- 文件名：简体中文必须 `_l_simp_chinese.yml` 结尾；覆盖原版文本放 `localisation/<lang>/replace/`（文件夹名**必须恰好是 replace**）
- 违反编码规则**不报错，只静默不生效**（文本显示原始 key）——这就是为什么每步都要验证
- 全文规则路由 **hoi4-mod-workflow**（本表是速查）

## 文件依赖链（先定义后引用）

```
本地化占位 → GFX 注册 → 想法(idea) → 人物(character) → 国策(focus) → 事件(event) → 决议(decision) → 国家 history
```

- 每个文件在清单中标注**依赖链序号**（1~8）
- 加载顺序：`00_` 前缀文件先于 `zz_` 加载；**同名文件=整个文件被后加载的覆盖**
- 国策引用 idea/事件时，被引用的必须先定义（否则报错或静默失败）

## 计划文档结构

### 计划头模板

```markdown
# [功能名] 实施计划
**REQUIRED SUB-SKILL:** hoi4-executing-plans
- [ ] 所有步骤完成并验证后，转 hoi4-verification-before-completion

**目标版本:** 1.17.x        ← 版本锚定（必写）
**目标:** <一句话，可测量>
**架构:** <涉及系统与文件组织>
**模板来源:** hoi4-templates → <具体模板名>   ← 每步的模板出处
```

### Scope Check

- 功能含**多个独立子系统**（如"新国家 + 阵营系统"）→ **拆成多个计划**，各产出"可加载可验证"的中间成果
- 每个计划聚焦一个可独立验证的功能

### File Structure（文件清单先行）

每个文件一行：

```
| 依赖序号 | 文件路径（精确） | 职责 |
|---|---|---|
| 1 | localisation/simp_chinese/ger_l_simp_chinese.yml | 全部新 key 本地化（占位先行） |
| 2 | common/national_focus/ger_focus.txt | 德国新国策树 |
| 3 | common/events/ger_events.txt | 触发事件（注意：事件在 `common/events/`，不是根级 `events/`） |
```

### 步骤粒度 = 最小可加载单元

- **一个步骤 = 一个可独立验证的单元**（如"一个国策 + 它的奖励 + 本地化 + 图标引用"），不是按时间切
- 步骤顺序 = 文件依赖链顺序
- 每步完成后游戏可加载、可验证该步成果（国策可见可点击 / 事件可触发）

### Task Structure

```markdown
### 步骤 N：<单元名>

**Files:** common/national_focus/ger_focus.txt, localisation/.../ger_l_simp_chinese.yml

- [ ] 写国策块（完整代码，无占位符）+ 本地化条目
- [ ] **验证**: CWTools 校验（期望 0 错误）→ `-debug` 启动进主菜单 → 退出 → 清空并重读 error.log（期望无本 mod 条目）→ 游戏内国策可见可点击
```

每步三件套：**完整代码块 + 精确命令 + 期望输出**。

### 无占位符纪律

- 禁止 `TBD`、`TODO`、`<适当处理>`、"类似上一步"等
- 每个代码块都必须是**可直接落盘的完整内容**（TAG 已替换为真实值）
- 期望输出要具体（"error.log 无新增；游戏内国策可见可点击"），不能写"应该没问题"

## Self-Review（写完后自查，5 项）

1. **依赖链完整性**：文件清单覆盖依赖链全链（含 `add_namespace` 若有）、引用全有先定义
2. **本地化全覆盖**：每个新 key 都有本地化条目，无遗漏
3. **重复 ID + 占位符扫描**：无重复国家 tag / 无未替换 TAG / 无 TBD
4. **BOM 逐文件核对**：每个文件按编码短表核对（yml 带 BOM、txt/descriptor 无 BOM）
5. **版本语法核对**：无 1.14/1.16 旧语法混入；`supported_version` 两个点

## 保存与交接

- 保存到 `docs/superpowers/plans/YYYY-MM-DD-<功能名>.md`（用计划编写时的项目内路径）
- **出口**：计划完成并自查通过 → 交给 **hoi4-executing-plans** 执行

## 常见错误表

| 现象 | 原因 | 修复 |
|---|---|---|
| 国策名显示原始 key | 文件清单缺本地化步骤 | 每步强制含本地化文件（依赖链 1 号位） |
| 国策引用的 idea/事件不存在 | 引用未先定义（依赖链错序） | 被引用的内容排在被引用之前 |
| 本地化整文件不加载 | yml 漏 BOM 或漏首行 `l_simp_chinese:` | 编码短表逐文件核对 |
| 图标不显示 | GFX 引用未注册 sprite（或大小写不一致） | 图标引用列进依赖链 2 号位 |
| 一步做完无法验证 | 步骤粒度太大（按时间切而非按单元切） | 拆成最小可加载单元 |
| `Invalid supported_version` | supported_version 写成三个点 | 用 `"1.17.*"` 或 `"1.17.3"` |
| 计划执行到一半发现文件路径不存在 | 路径写的是 mod 外假设路径 | Files 行用精确路径并写清 mod 内相对位置 |

## Reference Files

| 文件 | 内容 | 何时读 |
|---|---|---|
| `references/02-格式约定与本地化.md` | 本地化语法、特殊字符、descriptor、defines 覆盖、编码陷阱表 | 写计划中的代码块/本地化/编码时 |
| `references/04-制作注意事项与最佳实践.md` | 制作注意事项与最佳实践 | 写计划前过一遍防坑 |

## Cross-Reference

**依赖**：
- **hoi4-mod-workflow** → 编码规则全文、文件依赖链、目录结构
- **hoi4-code-reference** → 语法确认（effect/trigger/scope）
- **hoi4-templates** → 具体模板骨架（计划头"模板来源"）
- **hoi4-debugging** → 预期错误对照（验证点写期望输出时）

**被引用**：
- **hoi4-brainstorming** → 唯一出口指向本 skill
- **hoi4-executing-plans** → 执行的唯一入口
- **hoi4-systematic-debugging** → 根因指向计划缺陷时回本 skill 修订

## 出口

计划完成 + Self-Review 通过 → 交给 **hoi4-executing-plans**。
