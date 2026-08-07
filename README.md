# HOI4 MOD 开发技能体系

6 个 AI Agent 技能，让 Claude Code / Codex / Copilot CLI / Gemini CLI 能正确地创建、调试、迁移 Hearts of Iron 4 模组。

## 架构

```
hoi4-modding（入口路由）
  ├── hoi4-code-reference  ← 代码速查：effects, triggers, modifiers, scopes
  ├── hoi4-templates       ← 模板库：46 个可复制的文件骨架
  ├── hoi4-mod-workflow    ← 制作工作流：文件依赖顺序 + 验证清单
  ├── hoi4-version-migration ← 版本迁移：1.15 / 1.17 / NCNS 兼容指南
  └── hoi4-gui-shader      ← GUI/Shader 进阶：containerWindow, gridbox, HLSL
```

两层模型：

- **Superpowers 层（流程）**：brainstorming → writing-plans → executing-plans → verification —— 管"怎么做事"
- **HOI4 层（领域）**：这 6 个 skill —— 管"写什么代码"

## 素材来源

全部知识从 [秋起图书馆（霜泽图书馆）](https://steamcommunity.com/sharedfiles/filedetails/?id=3445449478) 提取，总计约 2670 个文件：

| 素材 | 路径 | 用途 |
|---|---|---|
| 代码提词器（37 文件） | `基础代码/代码提词器/` | effects/triggers/modifiers 大全 + 官方文档镜像 |
| 常用代码模板（46 文件） | `基础代码/常用代码模板/` | 国策/事件/人物/内阁/军队/装备/GUI 模板 |
| 代码教程（51 文件） | `基础代码/代码教程合集/` | PDF/DOCX/TXT 教程 |
| GUI 进阶（43 文件） | `高级代码/GUI/` | 超事件模板、scripted GUI 模板 |
| Shader（11 文件） | `高级代码/Shader/` | HLSL 教程 + 曲线 shader 案例 |
| 版本适配（2 PDF） | `版本适配指南/` | v1.15 迁移指南 + NCNS 兼容指南 |

## 制作方法

### 总体方法：TDD 应用于文档

遵循 **writing-skills** 的 RED-GREEN-REFACTOR 循环——和写代码一样的 TDD，但测试对象是 Agent 的行为而非代码逻辑。

| TDD 概念 | 在 Skill 创建中的映射 |
|---|---|
| **测试用例** | 压力场景（给子 Agent 出题） |
| **测试先写** | 先跑基线，确认 Agent 会犯错 |
| **测试失败（RED）** | 文档化 Agent 在没有 skill 时编造什么 |
| **生产代码** | SKILL.md |
| **测试通过（GREEN）** | 加载 skill 后 Agent 不再犯同样的错 |
| **重构** | 发现新的 rationalization → 明确堵上 |

**铁律：没有一个 skill 可以在没有失败测试证明需要它之前被写出来。**

### RED 阶段：基线测试

以 `hoi4-code-reference` 为例。在没有 skill 的情况下问 Agent：

> "我想查一下有没有一个 modifier 能提高核心领土上的攻击力。叫什么名字？"

**Agent 的实际回答**：`core_attack = 0.10`

**正确答案**：`army_core_attack_factor`

Agent 编造了一个 plausible 但游戏中不存在的 modifier 名称。这个错误证明了 skill 的必要性——没有参考，Agent 就会猜，猜错了 MOD 在游戏里静默失效。

### GREEN 阶段：写最小 Skill

针对 RED 阶段发现的具体失败来写。SKILL.md 的结构遵循 writing-skills 规范：

**Frontmatter（YAML）**：
```yaml
---
name: skill-name-with-hyphens
description: Use when [触发条件——只写何时用，绝不总结工作流]
---
```

**关键 SDO 规则**：description 只写触发条件，不写工作流总结。因为测试发现，如果 description 总结了工作流，Agent 会跟着总结走而不去读完整 skill 内容。

**正文结构**：
- Overview（核心原则，1-2 句）
- Quick Reference（内联速查表）
- Reference Files（附属文件索引）
- How to Use / Search
- Common Mistakes
- Cross-Reference 到其他 skill

### 匹配失败类型来选择指导形式

writing-skills 里有一条关键的"Match the Form to the Failure"规则：

| 基线失败类型 | 正确形式 | 错误形式 |
|---|---|---|
| 跳过/违反规则（知道但不做） | 禁令 + 合理化表 + Red Flags | 软指导（"建议……"） |
| 输出形状不对（啰嗦/跑偏） | 正向配方/契约：声明输出是什么 | 禁令列表（"不要写 X"） |
| 漏掉必须元素 | 结构化的 REQUIRED 字段 | 正文提醒 |

对于 HOI4 技能，**代码速查和模板库是 Reference 型**——用配方式契约（输出格式明确，不啰嗦"不要编造"）。**工作流是 Technique 型**——用依赖顺序 + Red Flags 抵抗"跳过步骤"的冲动。

### 交叉引用：不用 @ 链接

Skill 之间互相引用时，只用 skill 名称，不强制加载：

```
✅ "For syntax lookups, use **hoi4-code-reference**"
❌ "@skills/hoi4-code-reference/SKILL.md"（强制加载烧 200k+ context）
```

### REFACTOR 阶段：堵漏洞

写完 skill 后重新测试，Agent 可能找到新的 rationalization。每发现一个就加 explicit counter：

```
Red Flags — STOP and Recheck:
- Creating a file that references an ID you haven't defined yet
- You skipped GFX registration
- You are editing files in the game install directory instead of the mod folder
```

### 验证结果

| 测试 | 无 Skill (RED) | 有 Skill (GREEN) |
|---|---|---|
| 核心领土攻击 modifier? | `core_attack = 0.10` ❌ | `army_core_attack_factor` ✅ |
| 加 100 政治点数的 effect? | `add_political_power = 100` ✅ | `add_political_power = 100` ✅ |
| 国策模板格式? | 遗漏 `focus_tree = { id = ... }` 外层 ❌ | 完整复制模板 ✅ |

## 跨平台兼容

SKILL.md 基于 [agentskills.io](https://agentskills.io) 规范，全平台通用：

| 平台 | Skill 加载方式 | 安装路径 |
|---|---|---|
| Claude Code | `Skill` 工具 | `~/.claude/skills/` |
| Codex (OpenAI) | 原生加载 | `~/.agents/skills/` |
| Copilot CLI | `skill` 工具，自动发现 | `~/.agents/skills/` |
| Gemini CLI | `activate_skill` 工具 | `~/.agents/skills/` |

安装方式：把 6 个 `hoi4-*` 目录复制到对应路径即可，零配置。

## 与 Superpowers 的衔接

这些 HOI4 skill 设计为与 Superpowers 流程 skill **协作**：

```
用户: "帮我在 MOD 里加一个新国家"

brainstorming  → "这个国家背景是什么？"
writing-plans → 加载 hoi4-mod-workflow → 拆解为文件清单
executing-plans → 子 Agent 加载 hoi4-code-reference + hoi4-templates → 并行写文件
verification → 加载 hoi4-mod-workflow 的 checklist → 检查 error.log
```

Superpowers 管节奏和纪律，HOI4 Skill 管具体执行内容。

## 如何复现这个方法

如果你的领域也需要给 Agent 做技能体系：

1. **素材整理**——把你积累的参考资料、模板、教程按类别归类
2. **RED**——给 Agent 出题，记录它在没参考时的错误和编造
3. **GREEN**——针对每个错误写最小 SKILL.md，用配方式契约而非禁令
4. **REFACTOR**——重新测试，补上新发现的漏洞
5. **写入口 Skill**——一个轻量路由表串联所有子 skill

想了解完整的 skill 创作方法论，读 [writing-skills](https://agentskills.io) 和 [superpowers:test-driven-development](https://github.com/anthropics/claude-code)。

## 文件结构

```
hoi4-skills/
  README.md                     ← 本文档
  hoi4-modding/SKILL.md         ← 入口路由（152 词）
  hoi4-code-reference/SKILL.md  ← 代码速查（1635 词）
    references/                 ← 30 个引用文件（提词器 + 官方文档）
  hoi4-templates/SKILL.md       ← 模板库（681 词）
    templates/                  ← 46 个模板文件
  hoi4-mod-workflow/SKILL.md    ← 制作工作流（782 词）
  hoi4-version-migration/SKILL.md ← 版本迁移（446 词）
  hoi4-gui-shader/SKILL.md      ← GUI/Shader 进阶（584 词）
```

## 作者

韩瑀羲 (Han Yuxi)，2026-08-06

基于秋起图书馆（霜泽图书馆）的 HOI4 MOD 制作教程和工具合集构建。
