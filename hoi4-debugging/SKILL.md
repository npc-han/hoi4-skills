---
name: hoi4-debugging
description: Use when 遇到 HOI4 钢4 游戏报错、崩溃/闪退（CTD）、功能没生效（静默失败）、error.log 看不懂、需要查日志位置/启动参数/console 命令或排查定位问题。Also use when error.log 报错条目需要解读、崩溃后需要二分法定位或 Nudge 修复建筑崩溃。
---

# HOI4 报错与排查（知识字典）

## Overview

**管什么**：HOI4 报错的"是什么"——错误字典（error.log 条目 → 原因 → 修复）、日志位置、启动参数、console 命令、崩溃定位方法。

**何时用**：
- 游戏报错、崩溃/闪退、功能没生效，需要解读报错含义
- 需要定位日志位置、启动参数（`-debug`/`-crash_data_log`）、console 命令
- 需要二分法或 `-crash_data_log` 的 `LastRead` 定位崩溃源

**何时不用**：
- 需要**系统化修复**（连续失败、根因不明）→ **hoi4-systematic-debugging**（三失败模式判定 + 四阶段循环）
- 纯语法查询（effect/trigger/scope 怎么写）→ **hoi4-code-reference**
- 文件放哪、编码规则、依赖顺序 → **hoi4-mod-workflow**
- 版本间语法变化 → **hoi4-version-migration**

**版本锚定**：以下规则基于 1.17.x。排查时如混用 1.14（MIO）/1.16（核心圈）旧语法，先对照 **hoi4-version-migration** 的变化表。

## Quick Reference

### 三失败模式判定（第一件事）

| 失败模式 | 症状 | 定位入口 |
|---|---|---|
| **CTD（崩溃/闪退）** | 游戏直接退出 | 读 `setup.log` 最后读取的文件 → 用 `-crash_data_log` 重跑拿 `crashes/meta.yml` 的 `LastRead: <文件> (<行号>)` → 对照 LastRead 表 → 二分法 |
| **error.log 硬错误** | 有报错条目 | 看 `[xxx.cpp:行号]` 前缀定类型；条目内路径/行号**指向你的文件** → 查下方字典 |
| **静默失败** | 日志干净但功能不生效 | 走静默失败 12 项检查表（最危险，最花时间） |

### error.log 条目解读

格式：`[cpp名:行号] 错误原文 at <文件路径>:<行号>`。cpp 名 = 错误类型：

| cpp 名 | 类型 |
|---|---|
| `effect.cpp` | effect 错误（拼写 / trigger 当 effect / 缺花括号） |
| `trigger.cpp` | trigger 错误 |
| `pdxscript.cpp` | 解析错误（括号不闭合、引号未闭合） |
| `texturehandler.cpp` | 贴图缺失/路径错 |
| `gui.cpp` | GUI/sprite 未注册 |
| `eventmanager.cpp` | 事件系统错误 |
| `map/...` | 地图/州数据错误 |

### 报错字典（核心 6 类）

**1. 版本号/描述符类**
| 错误原文 | 原因/修复 |
|---|---|
| `Invalid supported_version in file: mod/xxx.mod line: 10` | 版本格式错。用 `"1.17.*"` 或 `"1.17.3"`——**两个点**，三个点必错 |
| `Incorrect MOD descriptor: "mod/xxx.mod"` | .mod 畸形。根目录 .mod 应含 `path=`；文件夹内 descriptor.mod **不应含** `path` |

**2. 国家 tag 类**
| 错误原文 | 原因/修复 |
|---|---|
| `SOV - is not in the tag list` | 引用了不存在的国家 tag。检查拼写（大写）与是否已定义 |

**3. 本地化类**
| 错误原文 | 原因/修复 |
|---|---|
| `The game has loc key collisions. Check logs/text.log` | 本地化键冲突（两个文件定义同一 key）。**查 text.log**，别只看 error.log；覆盖原版键用 `localisation/<lang>/replace/` |
| 国策/事件名显示 "undefined" | 缺本地化条目或 yml 未加载（编码/BOM/文件名问题，见 hoi4-mod-workflow） |

**4. GFX/纹理类**
| 错误原文 | 原因/修复 |
|---|---|
| `Icon definition "" ... does not start with "GFX_"` | 图标引用无效：既非文件路径也非 `GFX_` sprite 条目 |
| `Texture ... .dds have forbidden compression, have you tried DXT3?` | .dds 压缩格式不允许，用 DXT3 重新导出 |
| `Could not find sprite type [GFX_...]` | spriteType 未注册。在 `interface/*.gfx` 注册同名条目，**大小写敏感** |
| `Couldn't find texture file: ...` | 贴图文件缺失/路径错。检查正斜杠 `/` 与文件名 |
| `Could not find music named ...` | 音乐引用缺失 |

**5. trigger/effect 类**
| 错误原文 | 原因/修复 |
|---|---|
| `Invalid trigger 'XXX' in common/ideas/xx.txt line : 7` | trigger 拼写错误或不存在。查 hoi4-code-reference 的 triggers 文档 |
| `Invalid effect 'XXX' in ... line : 327` | effect 拼写错误，或**把 trigger 写在 effect 位置**。查 effects 文档 |
| `Non assign effect is not enclosed in {}: set_capital` | 需要块参数的 effect 没写花括号 |
| `Unexpected token` | 结构性错误（括号/引号未闭合）。**报错行号可能误导**——实际错误常在别处（每次重试行号上移一行 = 前面有未闭合括号） |
| `has_completed_focus = XXX` 校验失败 | 引用的国策 ID 不存在。注意：**国策执行期间该国策未标记完成**，其内部 `has_completed_focus` 恒为 false |

**6. 州/地图类**
| 错误原文 | 原因/修复 |
|---|---|
| `State #743 does not have a category defined` | 新州缺 `state_category = ...` 和 `manpower = ...` |
| `map/buildings.txt error: ... location is not within specified state` | 建筑指向错误州。用 Nudge 重算/重分配 |
| `Attempting to set capital state #563 for X, they don't own it!` | 首都设在国家不拥有的州 |
| `Map invalid X crossing. Please fix pixels` | 四省共享一角点（地图横向循环，边界处也会出现） |
| `Province X has TOO LARGE BOX` | 省宽/高超过地图 1/8（两省意外共色或省过大） |
| `Bitmap and province definition disagree ... coastal` | provinces.bmp 与 definition.csv 不一致，或 **definition.csv 缺行导致颜色-ID 映射全部错位** |

### 静默失败 12 项检查表（日志干净但功能没生效）

1. **编码/BOM**：yml 必须有 BOM，txt/descriptor.mod 不能有 BOM——yml 无 BOM 整文件不加载，游戏显示原始 key
2. **yml 首行**：必须是 `l_simp_chinese:`（本地化文件）
3. **本地化键冲突**：查 `logs/text.log`（不是 error.log）
4. **GFX 注册**：每个 icon = `GFX_xxx` 在 `interface/*.gfx` 有同名 spriteType，**大小写敏感**
5. **依赖链逆查**：功能不生效 → 查引用的 idea/事件/国策是否**先定义**（本地化占位→GFX→想法→人物→国策→事件→决议→history）
6. **加载顺序**：同名文件整体覆盖——`00_` 早于 `zz_` 加载，后加载覆盖先加载
7. **命名占用**：ID/文件名与已有内容重复（被原版或其他 mod 覆盖）
8. **版本语法**：用了当前版本不支持的旧语法（1.14/1.16 混入 1.17）
9. **supported_version**：写成三个点或旧版本号，游戏可能拒载
10. **占位符残留**：TAG/TBD 等未替换（国策/事件 ID 无效）
11. **热重载假设**：common/ 内容基本需**重启游戏**；热重载仅限本地化/界面类
12. **空文件被跳过**：游戏会跳过空文件——同样的问题在日志里可能显示为不同文件

### 日志位置与用途

| 日志 | 位置 | 用途 |
|---|---|---|
| error.log | `Documents/Paradox Interactive/Hearts of Iron IV/error.log` | 主报错日志（需 `-debug` 才完整） |
| crashes/ | 同上 | 崩溃信息（含 meta.yml 的 LastRead） |
| logs/text.log | 同上 | 本地化冲突 |
| logs/game.log | 同上 | 控制台文档（`trigger_docs` 写入） |
| setup.log | 同上 | 加载顺序，崩溃定位 |

### 启动参数（Steam 启动选项）

| 参数 | 用途 | 注意 |
|---|---|---|
| `-debug` | error.log 记录完整报错 | **开发期间必须用**，部分报错不开不记录 |
| `-crash_data_log` | crashes/meta.yml 记录崩溃前最后读取代码行 `LastRead: map/supply_nodes.txt (727)` | **显著拖慢游戏，仅崩溃调试用** |

### LastRead 对照表（崩溃时 meta.yml 最后读取的文件 → 常见根因）

| LastRead 文件 | 常见根因 |
|---|---|
| `common/countries/cosmetic.txt` | 完全覆盖了 `common/national_focus/` 或 `common/continuous_focus/`（数据库缺条目） |
| `map/rocketsites.txt` | 完全覆盖了 `history/states/` 或 `common/unit_leader/`（states 没有 generic 文件） |
| `common/national_focus/*.txt` | `shared_focus = my_focus` 引用不存在的国策 |
| `gfx/models/supply/railroad.shader` | provinces.bmp 错误：尺寸非 256 倍数、超 40 MiB、各 bmp 尺寸不一致、DIB 头格式错 |
| `history/general/*.txt` 等 | replace_path 删掉了原版必需文件 |

### 二分搜索法

- 崩溃/报错无法定位 → 分批移除 mod 内容，每批测一次，收敛到出问题的一组
- 用 `replace_path` 覆盖整个文件夹时**必须自带 generic 文件**（如 `common/national_focus/`、`common/unit_leader/`），否则数据库缺条目崩溃
- 二分时可分批移除 `replace_path`（**`history/states/` 和 `map/strategicregions` 除外**——没有 generic 文件，必须手写）
- **两个 .mod 文件都要编辑**：根目录 `.mod` 和文件夹内 `descriptor.mod` 都写 `replace_path = "xxx"` 才生效

### Nudge 修复建筑崩溃

`map/buildings.txt error: ... location is not within specified state` → 在游戏内用 Nudge 工具（调试模式）重算/重分配建筑位置，而不是手改 buildings.txt 猜测。

### console 命令速查

| 命令 | 用途 |
|---|---|
| `tdebug` | 显示 tooltip/ID 调试信息 |
| `reload localisation` / `reload ideas` 等 | 热重载部分内容（非全部） |
| `event <id>` | 强制触发事件测试 |
| `tag <TAG>` | 切换国家 |
| `observe` | 观战模式（测试 AI） |
| `Focus.AutoComplete` | 立即完成国策 |
| `trigger_docs` | 生成 trigger 文档写入 logs/game.log |

### 修复金律（03-报错与排查.md 第 6 节）

1. 报错里的文件路径和行号**指向你的文件**，先去那里看
2. 修完**清空 error.log 再测**，否则新旧报错混在一起
3. 所有 `common/` 文件夹内的报错都应修复，不能跳过
4. 排查崩溃用 `-crash_data_log`，**平时不要开**（拖慢游戏）

### 环境问题速查

| 症状 | 原因/修复 |
|---|---|
| error.log 大量奇怪错误，游戏异常 | **用户目录含非 ASCII 字符**（游戏不理解 UTF-8）。把用户目录/Documents 重定向改名为纯 ASCII |
| "the system cannot find the path specified" | 备份 mod → 退订全部 mod → 删 `Documents/paradox/Hearts of Iron IV/mod/` → 删 `mods_registry.json` 和 `launcher-v2.sqlite` → hoi4.exe 加入杀毒白名单 → 重新订阅 |
| 修改不生效 | Steam → 右键游戏 → 属性 → 本地文件 → 验证游戏文件完整性 |

## 常见错误表

| 现象 | 原因 | 修复 |
|---|---|---|
| `Unexpected token` 行号指向看似正确的地方 | 报错行号误导——实际错误在别处（前面有不闭合括号），重试行号每次上移一行 | 从文件头检查括号/引号闭合，不要只改报错行 |
| `Invalid effect 'XXX'` | 把 trigger 写在 effect 位置（或拼写错误） | 查 hoi4-code-reference 确认该关键字是 effect 还是 trigger |
| 本地化键冲突，但 error.log 没显示 | 冲突记录在 `logs/text.log`，不在 error.log | 查 text.log；覆盖原版键用 `localisation/<lang>/replace/` |
| 国策/事件名显示原始 key（如 `GER_focus_x`） | yml 无 BOM 或首行不是 `l_simp_chinese:`，整文件不加载 | 补 BOM（UTF-8 with BOM），首行写 `l_simp_chinese:` |
| 图标不显示，报错 `Could not find sprite type` | `GFX_xxx` 未在 `interface/*.gfx` 注册，或大小写不一致 | 注册同名 spriteType，核对大小写 |
| 游戏闪退但不读 error.log | CTD 的线索在 setup.log 与 crashes/meta.yml | 读 setup.log 最后加载文件；`-crash_data_log` 重跑拿 LastRead |

## Reference Files

| 文件 | 内容 | 何时读 |
|---|---|---|
| `references/03-报错与排查.md` | 报错字典完整版（中英对照 7 类）、LastRead 对照表、replace_path 陷阱、环境问题、排查流程总纲、修复金律 | 查字典原文、崩溃定位细节、replace_path 二分搜索时必读 |

## Cross-Reference

**依赖**：
- **hoi4-code-reference** → `references/常见报错解读.md`（12 类报错分类与静默失败清单基线）、effects/triggers documentation
- **hoi4-mod-workflow** → 编码/BOM 规则、文件依赖链、加载顺序
- **hoi4-templates** → 可工作参考实现（对照"应该长什么样"）
- **hoi4-version-migration** → 版本间语法变化（1.14/1.16/1.17）

**被引用**：
- **hoi4-systematic-debugging** → 三失败模式判定后查本字典（分工：本 skill 答"这个报错是什么"，systematic-debugging 答"怎么系统修好"）
- **hoi4-verification-before-completion** → error.log 零新增判断归属
- **hoi4-executing-plans** → 单元验证失败时报错解读
- **hoi4-modding** → 路由表"报错/崩溃"场景

## Process Interface

任务超出单点查询（涉及多文件改动、需要设计新系统）时，先走 **hoi4-brainstorming** → **hoi4-writing-plans** 流程链，本 skill 只做报错知识支撑。
