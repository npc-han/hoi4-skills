# HOI4 Skills 更新日志

## 第二次更新 (2026-08-06) — 基于 Wiki/社区资料的全面扩展

### 更新动机

初版 skill 在基线测试（RED 阶段）中暴露了两个核心问题：
1. Agent 不知道 HOI4 modding 的基础实操知识（.mod 文件结构、编码规则、调试方法）
2. Agent 对 PDXScript 高级语法概念（scope 链、event target、flag 系统）缺乏系统认知

本次更新基于 HOI4 官方 Wiki (hoi4.paradoxwikis.com) 和社区文档，系统性地补充了初版遗漏但 Agent 实际需要的内容。

---

### hoi4-mod-workflow（最大改动：124 行 → 336 行，+212 行）

| 初版已有 | 第二次新增 |
|---|---|
| 依赖顺序图（8 步） | **Mod 文件结构详解**：.mod vs descriptor.mod 对比表、replace_path/dependencies/user_dir/supported_version 字段说明、路径分隔符规则（正斜杠 ≠ 反斜杠）、目录结构树 |
| 3 个功能开发指南 | **Mod 类型说明**：Minor Mod / Major Mod (Total Conversion) / Sub-Mod 的区别和配置 |
| Mod Setup Checklist（5 项） | **文件编码规则表**：6 种文件类型 × 正确编码 × 关键注意事项；VS Code/Notepad++ 设置方法；localisation .yml 专项规则（首行 l_english:、ASCII key、replace 文件夹覆盖） |
| Verification Checklist（6 项） | **文件命名与加载顺序**：ASCII 排序规则、覆盖 vs 添加、defines 特殊规则（禁止复制 00_defines.lua） |
| Common Mistakes（6 条） | **调试与故障排除**（全新章节）：7 种日志文件用途表、2 个启动参数、按时间分类的 Crash 原因表（启动/选国家/游戏中）、二分搜索调试法、Nudge! 工具使用步骤 |
| Red Flags（5 条） | **控制台命令大全**（16 个命令）：tdebug / reload / event / tag / observe / aiview / Focus.AutoComplete 等 |
| | **开发工具推荐**（5 个工具）：VS Code + CWTools、HOI4 Mod Utilities、Irony Mod Manager、WinMerge、GIMP |
| | **扩展 Verification Checklist**（+2 项）：-debug 模式测试、.yml 首行检查 |
| | **扩展 Common Mistakes**（+4 条）：反斜杠路径、复制整个 defines、编码错误、缺 l_english: |
| | **扩展 Red Flags**（+5 条）：未用 -debug、反斜杠、.yml 缺头、defines 全复制、跳过本地化 |

---

### hoi4-code-reference（188 行 → 309 行，+121 行）

| 初版已有 | 第二次新增 |
|---|---|
| Scope 表（12 个基础 scope） | **PDXScript 语言基础**：Effects / Modifiers / Triggers / Scopes 四类关键字区别表；执行顺序规则；静默忽略规则 |
| Top 20 Effects（国家作用域） | **Scope 链式引用**：FROM.FROM / PREV.PREV / FROM.PREV 等 6 种链式语法；FROM 在事件触发时的向下传递规则；链超过 3 层警示 |
| Top 20 Triggers | **Event Target 系统**：save_event_target_as / save_global_event_target_as / save_temporary_scope_as 对照表；使用语法；变量作为 scope 引用 (var:variable_name) |
| 常用 Modifier 代码（24 个） | **Flag 系统**：5 种 flag 类型表（country/global/state/character/MIO）；1.12+ 扩展语法 {flag=x days=365 value=3}；flag 类型必须匹配的规则 |
| Reference Files 索引 | **国策筛选器标签**：10 个 FOCUS_FILTER_* 标签及对应分类 |
| Common Mistakes（7 条） | **科技树语法速查**：11 个关键字段表（research_cost / start_year / path / enable_equipments 等） |
| | **扩展 Common Mistakes**（+5 条）：available 块混用 effect、flag 类型不匹配、FROM 链跨事件、未识别关键字静默失败、缺 add_namespace |

---

### hoi4-version-migration（58 行 → 73 行，+15 行）

| 初版已有 | 第二次新增 |
|---|---|
| 1.17.3 vs 1.16.9 差异（外部路径） | **修正引用路径**：外部 `d:\mod zhizuo\...` → 本地 `hoi4-code-reference/references/...` |
| v1.15.x 迁移指南（外部 PDF） | **版本变更总览表**：1.11 到 1.17 每个大版本的关键变更（角色系统、学说精通、装备模块、MIO、特殊项目、核心圈、阵营系统） |
| NCNS 兼容指南（外部 PDF） | **扩展陷阱表**（+4 条）：replace_path 导致数据库缺失、CTD 排查、本地化 BOM 丢失、海军 OOB 格式 |
| Common Migration Pitfalls（6 条） | |

---

### hoi4-templates（115 行 → 124 行，+9 行）

| 初版已有 | 第二次新增 |
|---|---|
| 7 个模板分类 | **新分类 "Mod Setup & Localisation"**：descriptor.mod 指引、country_tags 指引、on_actions 指引、本地化模板入口、defines override 指引 |
| How to Use（5 步） | **本地化编码说明**：UTF-8 with BOM、首行 l_simp_chinese:、文件命名规则、replace 文件夹覆盖法 |

---

### hoi4-modding（19 行 → 20 行，+1 行）

| 初版已有 | 第二次新增 |
|---|---|
| 5 条路由 | **+2 条路由**：调试/编码规则/mod setup → hoi4-mod-workflow |

---

### 量化对比

| 指标 | 初版 | 第二次更新后 | 变化 |
|---|---|---|---|
| 总行数 | ~504 行 | **862 行** | +71% |
| 总词数（估算） | ~3,700 词 | **~6,400 词** | +73% |
| 覆盖主题 | 语法速查 + 模板库 + 工作流 + 迁移 | +调试 + 编码 + Mod结构 + 工具 + Scope链 + EventTarget + Flag + 筛选器 + 科技语法 + 版本总览 | +12 个主题 |
| 引用来源 | 秋起图书馆（中文教程） | 秋起图书馆 + HOI4 官方 Wiki + 社区文档 | 双源交叉验证 |

---

### 未改动部分

以下内容在初版中已较为完整，本次未做大幅修改：
- **hoi4-gui-shader** — GUI 组件速查、HLSL 参考、常见错误表均保留。外部路径依赖问题已记录但未修改（需用户手动迁移素材）。
- **hoi4-code-reference/references/** — 所有 30 个引用文件（提词器、官方文档镜像、Modifier 表）不变。
- **hoi4-templates/templates/** — 46 个模板文件不变。
- **README.md** — 保持不变。

---

## 第三次更新 (2026-08-06) — 报错解读能力

### 更新动机

用户实际使用 skill 制作 mod 时仍会遇到 bug/报错。用户观点：**报错不可避免，关键是让 skill 更好地理解报错的原因**。

前两次更新只回答了"报错去哪查"（日志位置、Crash 时机表），没有回答"**报错信息本身是什么意思**"。本次新增从报错文本反推原因的能力。

### 核心框架：三种失败模式

| 失败模式 | 信号 | 排查路径 |
|---|---|---|
| CTD（闪退） | 游戏直接退出 | setup.log 最后读取的文件 → 二分搜索 |
| error.log 硬错误 | 日志有条目（带文件+行号） | 按报错字典查原因 |
| 静默失败 | 功能不生效，日志干净 | 检查静默失败清单 |

### 改动明细

| 文件 | 改动 |
|---|---|
| **新建 `hoi4-code-reference/references/常见报错解读.md`** | 报错字典（核心交付物）：error.log 条目格式拆解（`[effect.cpp:402]: Invalid effect 'xxx' in 文件 line : 行号`）+ 12 类报错分类表（未知关键字/变量/作用域/解析/事件/对象缺失/本地化/GFX/GUI/国策科技/军队/CTD，每类含报错文本 → 根因 → 修复）+ 12 条静默失败清单 + 排查决策树。内容基于真实 error.log 格式（WebSearch 验证，非编造） |
| **hoi4-mod-workflow**（336 → 361 行） | Debugging 章节前新增 "Error Interpretation 报错解读" 小节：三种失败模式表、error.log 条目读取方法（含 3 条真实示例）、简版决策树、4 条黄金法则 |
| **hoi4-code-reference**（309 → 346 行） | ① 新增 "Error Message Quick Reference 报错速查" 表（12 条高频报错文本 → 原因）② Common Mistakes 表增加 "Error log shows" 列（每条错误写法对应什么报错/症状）+ 新增 2 行 ③ Reference Files 登记新字典 |
| **hoi4-modding**（20 → 21 行） | 路由表新增一行：error.log 报错解读 → hoi4-mod-workflow + 报错字典 |
| **CHANGELOG.md** | 追加本章节 |

### 与第二次更新的区分

- **第二次更新**：让 Agent 知道"怎么做出一个能跑的 Mod"（结构、编码、调试工具、控制台命令）—— 预防性知识
- **第三次更新**：让 Agent 知道"报错出现后怎么读懂它"（报错文本 → 原因 → 修复的完整字典）—— 反应性诊断能力
- 内容来源：第二次基于官方 Wiki 静态资料；第三次基于真实 error.log 报错格式 + 社区修复案例
