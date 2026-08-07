---
name: hoi4-systematic-debugging
description: Use when HOI4 钢4 mod 报错、游戏崩溃/闪退（CTD）、功能没生效（静默失败）需要系统化调试——三失败模式判定、根因调查四阶段循环、失败≥3 次 STOP 质疑架构。Also use when error.log 反复报错、修复试了多次没解决、用户说"帮我看看为什么没生效/为什么崩"。
---

# HOI4 系统化调试（Systematic Debugging）

## Overview

**管什么**：把 HOI4 报错**系统化修好**——三失败模式判定（第一动作）、四阶段调试循环（先找根因再修）、失败 ≥3 次 STOP。

**与 hoi4-debugging 的分工**：
- **hoi4-debugging**（知识型）答："这个报错**是什么**"（错误字典、日志位置）
- **本 skill**（流程型）答："怎么**系统修好**"（三失败模式、四阶段循环）

**何时用**：
- 报错/崩溃/没生效，需要系统化排查（尤其试了多次没解决）
- 从 hoi4-executing-plans 报错拦截门移交的系统性问题
- hoi4-verification-before-completion 发现 bug

**何时不用**：
- 单条报错、原因明确 → 直接查 **hoi4-debugging** 字典修掉
- 要加新功能 → **hoi4-brainstorming**

## 入口

来自 **hoi4-executing-plans** 报错拦截门（系统性问题移交）/ **hoi4-verification-before-completion** 发现 bug / 用户直接求助。

## Iron Law

**未完成根因调查，禁止提修复方案。**

"改改试试"就是失败模式。先回答"为什么会这样"，再回答"怎么改"。

## 三失败模式判定（第一动作）

收到报错**先判定失败模式**，再决定排查路线：

| 失败模式 | 判定 | 排查路线 |
|---|---|---|
| **CTD（崩溃/闪退）** | 游戏直接退出 | 读 `setup.log` 最后加载文件 → `-crash_data_log` 重跑拿 `crashes/meta.yml` 的 `LastRead: <文件> (<行号>)` → 对照 03-报错与排查.md 第 3 节表 → **二分法**（分批移除 replace_path，`history/states/` 与 `map/strategicregions` 除外——没有 generic 文件） |
| **error.log 硬错误** | error.log 有条目 | `[effect.cpp:402]` cpp 名 = 错误类型；条目内路径/行号**指向你的文件** → 查 **hoi4-debugging** 字典 |
| **静默失败（最危险）** | 日志干净但功能不生效 | 逐项查：① 编码/BOM（yml 无 BOM 整文件不加载）② **依赖链逆查**（国策没效果→查 idea 是否定义→查本地化/GFX）③ 命名占用/加载顺序（00_ 先 zz_ 后，同名整体覆盖）④ 版本语法（1.14/1.16 混入 1.17） |

## 四阶段循环

```
一 根因调查：读完整错误 → 复现（清空日志重测）→ git diff 查近期变更
             → 组件边界加诊断（tdebug / 事件日志）
二 模式分析：对照可工作参考（原版 / hoi4-templates / 资料库 13-完整示例工程）
三 假设验证：一次一个变量，每测清空日志
四 实施：单点修复 + 按复现路径验证
```

**铁律**：没走完阶段一，不进入阶段四。每测之间**清空 error.log**（否则新旧报错混入）。

## 失败 ≥3 次 STOP

同一问题尝试修复 **≥3 次**仍失败：
- **停止零散修复**，质疑架构（不是改更多行，而是想"设计对不对"）
- 回 **hoi4-brainstorming** 复审设计，或向用户如实报告进展
- 继续"再试一次" = 失败模式

## 修复金律（03-报错与排查.md 第 6 节）

1. 报错里的文件路径和行号**指向你的文件**，先去那里看
2. 修完**清空 error.log 再测**
3. 所有 `common/` 文件夹内的报错都应修复，不能跳过
4. 排查崩溃用 `-crash_data_log`，**平时不要开**（拖慢游戏）

## 出口

- 修复验证通过 → 回 **hoi4-executing-plans** 继续执行
- 根因指向计划缺陷 → 回 **hoi4-writing-plans** 修订计划
- 根因指向设计错误 → 回 **hoi4-brainstorming** 复审设计

## 常见错误表

| 现象 | 原因 | 修复 |
|---|---|---|
| `Invalid effect 'XXX'` | 把 trigger 当 effect 写 | 查 hoi4-code-reference 确认关键字类别 |
| 国策执行期内部 `has_completed_focus` 恒 false | 国策尚未标记完成 | 用其他条件判断（如 `has_focus_flag`） |
| `Unexpected token` 行号每次上移 | 实际错误在别处（前面有未闭合括号） | 从文件头查括号闭合 |
| replace_path 后启动崩溃 | 覆盖整个文件夹缺 generic 文件 | 自带 generic（history/states/ 必须手写） |
| 诡异报错无法解释 | 用户目录含非 ASCII 字符 | 用户目录改纯 ASCII（游戏不理解 UTF-8） |
| 本地化不生效但 error.log 干净 | 键冲突在 text.log 不在 error.log | 查 `logs/text.log` |
| 文本显示原始 key | yml 无 BOM 整文件不加载 | 补 BOM + 首行 `l_simp_chinese:` |

## Reference Files

| 文件 | 内容 | 何时读 |
|---|---|---|
| `references/03-报错与排查.md` | 报错字典完整版、LastRead 对照表、replace_path 陷阱、修复金律 | 三失败模式判定的对照参考（与 hoi4-debugging 的副本互指，内容一致） |

## Cross-Reference

**依赖**：
- **hoi4-debugging** → 错误字典（三失败模式判定后查；分工：它答"是什么"，本 skill 答"怎么修"）
- **hoi4-templates** → 可工作参考实现（模式分析阶段）
- **hoi4-code-reference** → 语法确认（trigger/effect 类别）
- **hoi4-version-migration** → 版本回归排查（1.14/1.16 语法变化）

**被引用**：
- **hoi4-executing-plans** → 报错拦截门移交
- **hoi4-verification-before-completion** → 发现 bug 移交
- **hoi4-modding** → 路由表"报错/崩溃"场景
- **hoi4-writing-plans** / **hoi4-brainstorming** → 失败 ≥3 次 STOP 的修订去向

## 出口

修复验证通过 → **hoi4-executing-plans**；根因指向计划/设计缺陷 → **hoi4-writing-plans** / **hoi4-brainstorming**。
