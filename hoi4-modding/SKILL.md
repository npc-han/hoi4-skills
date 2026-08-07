---
name: hoi4-modding
description: Use when creating or modifying Hearts of Iron 4 mods, working with Paradox script files (.txt, .gfx, .gui), or asked about HOI4 mod development — 钢4 制作总入口，路由到全部 hoi4-* skills。国策、事件、决议、人物、科技、地图、GUI、报错排查、发布等场景按流程链路由（设计→计划→执行→验证→调试→审查）。
---

# HOI4 Modding（路由中枢）

HOI4 钢4 mod 制作的所有请求都从这里路由。**先看流程路由表，再看知识路由表**——流程链（设计→计划→执行→验证→调试→审查）完全取代 superpowers，钢4 任务不依赖 superpowers 插件。

## 流程路由（Process Routing）— 优先级最高

| 触发场景（用户说…） | 路由目标 |
|---|---|
| 做个新国家 / 新系统 / 新机制 / 新国策树（设计未定） | **hoi4-brainstorming** |
| 设计 / 构思 / 想法 / 方案 / 头脑风暴（任何创作前） | **hoi4-brainstorming** |
| 设计已批准 / 有 spec / 多文件改动 / 写计划 | **hoi4-writing-plans** |
| 按计划执行 / 开始做 / 照着计划来（有 plan） | **hoi4-executing-plans** |
| 做完了 / 验证 / 确认没问题 / 检查一遍 / 提交前 | **hoi4-verification-before-completion** |
| 发布 / 上传创意工坊 / 更新到 Steam | **hoi4-verification-before-completion**（发布门 → **hoi4-publishing**） |
| 报错 / 崩溃 / 闪退 / 没生效 / 不加载 / 修 bug | **hoi4-systematic-debugging** |
| CTD / 白屏 / 进不去 / 启动就崩 | **hoi4-systematic-debugging** |
| 大型里程碑 / 发布前审查 / 交叉检查 / 审查报告 | **hoi4-review**（可选门） |
| 纯查询（语法/模板/报错含义/地图/发布知识） | 直接进知识路由表（不走流程链） |

## 知识路由（Quick Routing）

| When you need... | Load this skill | 何时返回流程链 |
|---|---|---|
| Effect/trigger/modifier syntax lookup | **hoi4-code-reference** | 语法查询是纯查询；若演变成多文件新功能，回 **hoi4-brainstorming** |
| Complete file templates (focus, event, idea, character, GUI...) | **hoi4-templates** | 同上 |
| Step-by-step mod creation workflow: what files to create in what order | **hoi4-mod-workflow** | 从零搭骨架后要加新内容，回 **hoi4-brainstorming** |
| Error dictionary: error.log entries, error message → cause → fix | **hoi4-debugging** | 单条报错直接修；反复报错/根因不明 → **hoi4-systematic-debugging** |
| Encoding rules (BOM), file dependency chain, load order | **hoi4-mod-workflow** | 涉及多文件结构改动，先 **hoi4-writing-plans** |
| Crash troubleshooting (CTD), binary search, LastRead | **hoi4-debugging** | 崩溃系统化定位 → **hoi4-systematic-debugging** |
| Mod setup basics: .mod file, descriptor.mod, directory structure, replace_path | **hoi4-mod-workflow** | 纯查询；要设计文件结构 → **hoi4-brainstorming** |
| Porting a mod to a newer game version or inter-mod compatibility | **hoi4-version-migration** | 迁移涉及多文件 → 先 **hoi4-writing-plans** |
| Custom GUI windows, scripted GUIs, or HLSL shader effects | **hoi4-gui-shader** | 新建 GUI 系统 → **hoi4-brainstorming** |
| 进阶系统: sp_project / 阵营 / 核心圈 / MIO / 权力平衡 / AI 战略 | **hoi4-advanced-systems** | 新系统设计 → **hoi4-brainstorming** |
| 地图制作: 省份 / 州 / 战略区 / 补给铁路 / 渲染 / Nudge | **hoi4-map-making** | 大改地图 → **hoi4-brainstorming** |
| 发布 / 创意工坊 / Python 工具链 | **hoi4-publishing** | 发布前先验证 → **hoi4-verification-before-completion** |

**REQUIRED BACKGROUND:** Before any creative mod work, invoke **hoi4-brainstorming** to clarify requirements. Before multi-file changes, invoke **hoi4-writing-plans** to create an implementation plan. Execute plans with **hoi4-executing-plans**. After writing code, invoke **hoi4-verification-before-completion** to run the mod verification checklist. For error/crash fixes, invoke **hoi4-systematic-debugging**. For large milestones before release, consider **hoi4-review**. (特调版流程链完全取代 superpowers——钢4 任务全程走 hoi4-* 链，不依赖 superpowers 插件。)
