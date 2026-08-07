---
name: hoi4-verification-before-completion
description: Use when 宣称 HOI4 钢4 mod 工作完成、修复完成、发布前检查——需要新鲜验证证据（error.log 零新增/本地化 100%/GFX 注册/BOM 编码/版本语法/playtest），证据先于断言，禁止无验证宣称完成。Also use when 用户说"做完了""确认没问题""检查一遍"或准备提交/发布。
---

# HOI4 完成前验证（Verification Before Completion）

## Overview

**管什么**：在宣称"做完了"之前，用**新鲜验证证据**证明——证据先于断言。钢4 版把验证变成可执行清单（error.log / 本地化 / GFX / BOM / 版本语法 / playtest）。

**何时用**：
- 计划执行完、准备宣称完成 / 提交 / 发布
- 用户说"做完了？""确认没问题""检查一遍"
- 修复后确认修复生效

**何时不用**：
- 还在执行中 → **hoi4-executing-plans**
- 功能有 bug 需要系统修复 → **hoi4-systematic-debugging**
- 大型里程碑发布前深度审查 → **hoi4-review**（本 skill 之后的可选门）

## 入口

来自 **hoi4-executing-plans** 出口（全部单元验证通过后）。

## Iron Law

**无新鲜验证证据，禁止宣称完成。**

- "新鲜"= 刚才运行的结果，不是上周、不是"之前验证过"
- 跳过任何一步 = 说谎，不是简化

## 5 步闸门（每项断言都要走）

```
IDENTIFY 证明命令（-debug 启动 / 读 error.log / grep 扫描 / BOM 检查）
→ RUN 新鲜跑一次
→ READ 完整输出（不看摘要，看原文）
→ VERIFY 输出证实断言（输出与断言逐条对应）
→ 才宣称完成
```

## 钢4 验证清单（8 项）

完整可勾选版见 `references/钢4验证清单.md`，摘要：

1. **error.log 零新增**：清空 → `-debug` 进主菜单 → 退出 → 重读，无本 mod 条目
2. **本地化覆盖 100%**：所有新 key 有条目；查 `logs/text.log` 无 key 冲突
3. **GFX 注册核对**：每个 icon = `GFX_xxx` 在 `interface/*.gfx` 有同名 spriteType，**大小写敏感**
4. **BOM 编码检查**：yml 带 BOM + 首行 `l_simp_chinese:`；txt/descriptor.mod 无 BOM；.mod 带 BOM
5. **版本语法核对**：`supported_version` 两位点；无 1.12/1.14 旧语法混用
6. **重复 id + 占位符扫描**：无重复国家 tag、未替换 TAG、TBD
7. **playtest 清单**：开局加载 → 目标国策可点击触发 → 事件弹出 → 决议可用 → 存档/读档正常；大型 mod 多轮平衡观察
8. **发布场景**（仅发布时）→ 路由 **hoi4-publishing**（descriptor 一致性、thumbnail、远程 ID）

**基线互指**：本清单以 hoi4-mod-workflow 的 Verification Checklist 为基线 + 钢4 扩展，两者互为引用不重复维护。

## 借口表（这些都不算证据）

| 借口 | 现实 |
|---|---|
| "error.log 没报错" | 没报错 ≠ 功能生效（静默失败：编码/BOM/依赖链问题不报错） |
| "游戏没崩溃" | 没崩溃 ≠ 内容加载了（可能整文件被跳过） |
| "上次验证过了" | 必须新鲜——文件可能改过、日志可能混入旧内容 |
| "只进了主菜单" | 主菜单 ≠ playtest。内容可见可点击才算数 |

## 输出：验证报告

每项写：**证据命令 + 输出摘录 + 结论**（通过/未通过）。未通过项不遮掩，如实报告。

## 出口

- 全部通过 → 允许宣称完成 / 提交 / 发布
- 大型 mod → 建议追加 **hoi4-review** 门
- 发现 bug → **hoi4-systematic-debugging**

## 常见错误表

| 现象 | 原因 | 修复 |
|---|---|---|
| 用上周的 error.log 当证据 | 不是新鲜证据 | 每次验证都重新清空再跑 |
| 只验证英文 locale | 漏了简体中文 | 中英 yml 都验证（含 `l_simp_chinese:` 首行） |
| error.log 内容不全 | 没开 `-debug`（部分报错不记录） | 验证必须 `-debug` 启动 |
| "CWTools 通过了" | CWTools 通过 ≠ 游戏内验证 | 游戏内 playtest 不可省 |
| "日志干净但功能不生效"直接宣称完成 | 忽略静默失败 | 走静默失败检查（编码/BOM/依赖链逆查） |

## Reference Files

| 文件 | 内容 | 何时读 |
|---|---|---|
| `references/钢4验证清单.md` | 8 项验证清单可勾选版（证据命令+期望输出） | 每次验证时逐项打勾 |

## Cross-Reference

**依赖**：
- **hoi4-mod-workflow** → Verification Checklist 基线、编码规则
- **hoi4-debugging** → error.log 条目归属判断（本 mod vs 无关）
- **hoi4-publishing** → 发布场景（清单 8）

**被引用**：
- **hoi4-executing-plans** → 出口指向本 skill
- **hoi4-modding** → 路由表"做完了/验证"场景
- **hoi4-review** → 内容审查复用本清单挑剔重跑

## 出口

验证通过 → 允许宣称完成；大型 → 建议 **hoi4-review**；发现 bug → **hoi4-systematic-debugging**。
