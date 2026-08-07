---
name: hoi4-executing-plans
description: Use when 执行已写好的 HOI4 钢4 实施计划——按依赖链顺序逐单元实现（国策/事件/决议/本地化/GFX），每单元 CWTools 校验与 -debug 验证门、error.log 报错拦截、阻塞处理、逐单元 git 提交。Also use when 有 plan 要落地、执行中报错需要判断自己修还是移交调试。
---

# HOI4 执行纪律（Executing Plans）

## Overview

**管什么**：把已批准的实施计划**按纪律执行**——批判性审阅计划、依赖链顺序逐单元实现、每单元验证门、报错拦截、阻塞处理。

**何时用**：
- 有一个已写好的实施计划（来自 hoi4-writing-plans）要执行
- 执行中遇到报错，需要判断"自己修"还是"移交调试"

**何时不用**：
- 没有计划 → 先 **hoi4-writing-plans**
- 设计还没定 → **hoi4-brainstorming**
- 计划全部完成 → 出口 **hoi4-verification-before-completion**

## 入口

来自 **hoi4-writing-plans** 的计划文档（含 REQUIRED SUB-SKILL 声明）。

## 执行流程

### 1. 批判性审阅计划（先审后写）

逐项检查计划：路径完整 / 依赖序正确 / BOM 标注 / 每步有验证点。**发现缺陷 → 停下补计划**（与用户确认），不静默跳过。

### 2. 按依赖链顺序逐单元执行（TodoWrite 跟踪）

每个单元（= 最小可加载单元，来自计划）循环：

```
取模板（hoi4-templates）→ 查语法（hoi4-code-reference）→ 写文件
→ 编码即时检查（BOM/首行）→ CWTools 快速校验
→ 攒批后 -debug 验证门（见操作卡）
→ 验证结果记录到计划 checkbox
```

- 执行序 = 依赖链序：本地化占位 → GFX 注册 → 想法 → 人物 → 国策 → 事件 → 决议 → history
- 编码即时检查表见 `references/启动与验证操作卡.md`

### 3. 报错拦截门

单元验证失败：
1. **先读 error.log**（先清空再测，防止旧报错混入）
2. 查 **hoi4-debugging** 字典明确错误含义
3. **明确原因才修**——修完清空日志重测
4. **系统性问题**（多个单元反复报错、根因不明）→ 移交 **hoi4-systematic-debugging**，不在执行位临时乱修

### 4. 阻塞处理

- 同一障碍 **≥3 次** → **停下问用户**，不再硬试
- 需要改计划 → 先获得用户批准（改计划 = 回到 hoi4-writing-plans 修订，或用户直接批准小改）

### 5. 提交纪律

- 每验证通过的单元做一次 git 提交（单人小步提交；团队按分支纪律）

## 出口

全部单元验证通过 → **hoi4-verification-before-completion**（大型功能可加可选 **hoi4-review** 门）。

## 常见错误表

| 现象 | 原因 | 修复 |
|---|---|---|
| 执行到一半发现计划路径是错的 | 不审计划直接开写 | 先批判性审阅，发现缺陷停 |
| 最后一起验证、一堆报错 | 跳过每单元验证攒到最后 | 每单元过验证门再进下一单元 |
| 写后不查编码直接进游戏 | 跳过编码即时检查 | 写文件即查 BOM/首行 |
| 报错时只盯着报错文本 | 不看自己的文件 | 报错里的路径/行号指向你的文件，先去那里看 |
| 修完测出"新报错"其实是旧的 | 旧 error.log 没清 | 先清空 error.log 再测 |
| 改到游戏本体目录 | 在游戏本体改文件 | **铁律：复制进 mod 目录再改**（中文社区铁律） |

## Reference Files

| 文件 | 内容 | 何时读 |
|---|---|---|
| `references/启动与验证操作卡.md` | 日志位置、-debug 参数、验证标准流程、热重载限定、编码即时检查表 | 每单元验证时 |

## Cross-Reference

**依赖**：
- **hoi4-templates** → 每单元取模板骨架
- **hoi4-code-reference** → 语法确认
- **hoi4-debugging** → 报错字典（拦截门）
- **hoi4-mod-workflow** → 编码规则全文
- **hoi4-systematic-debugging** → 系统性问题移交（不入执行位乱修）

**被引用**：
- **hoi4-writing-plans** → 出口指向本 skill
- **hoi4-verification-before-completion** → 入口来自本 skill 出口
- **hoi4-systematic-debugging** → 修复验证通过后回本 skill 继续执行

## 出口

全部单元验证通过 → **hoi4-verification-before-completion**。
