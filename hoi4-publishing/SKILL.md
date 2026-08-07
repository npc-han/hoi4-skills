---
name: hoi4-publishing
description: Use when 发布 HOI4 钢4 mod 到 Steam 创意工坊、上传/更新 mod、配置 descriptor.mod 与 .mod 文件、thumbnail/tags/remote_file_id 设置、使用 Python 工具链脚本（本地化添加/地图生成/文件格式化等）。Also use when 发布前检查 descriptor 一致性或了解自动化脚本用途。
---

# HOI4 工具与发布（Publishing）

## Overview

**管什么**：HOI4 mod 的发布与工具——创意工坊上传/更新流程、descriptor 文件细节、Python 自动化脚本工具链。

**何时用**：
- 要把 mod 发布到 Steam 创意工坊，或发布后更新维护
- 要配置 descriptor.mod / .mod 文件（name/path/tags/picture/supported_version/remote_file_id）
- 要用 Python 工具链脚本（本地化条目添加、地图生成、GFX 条目、文件格式化等）

**何时不用**：
- mod 编码规则/文件结构 → **hoi4-mod-workflow**
- 发布前的完整验证 → **hoi4-verification-before-completion**（发布场景路由本 skill）
- 发布前的大型审查 → **hoi4-review**（发布绑定审查）

**版本锚定**：`supported_version` 用 `"1.17.*"` 或 `"1.17.3"`（**两个点**，三个点必错）。

## Quick Reference

### 发布流程（必须走 launcher）

1. 启动器 → **Mods** 标签页 → **Mod Tools** → **Create Mod**：填名称、版本、目录（如 `mod/my_mod`）、tags
2. 启动器 → "All installed mods" → 选中 mod → **Upload mod** → 确认上传
3. **上传列表里没有你的 mod？** 说明它不是用 launcher 创建的：新建一个 mod，把你的文件复制进去，就会出现在列表里

### 文件要求（关键细节）

| 文件 | 要求 |
|---|---|
| 本地 `.mod` 文件 | 只给本地 launcher 用，**上传时不上传**；与 mod 文件夹内 descriptor.mod 信息一致 |
| `descriptor.mod` | 必须在 mod **文件夹内**，名字**必须恰好是 descriptor.mod**；⚠️ **不要用 UTF-8 BOM 保存**（与本地化 yml 必须带 BOM 相反！） |
| `thumbnail.png` | 可选；建议 1:1 比例、<1 MB |
| `tags` | 创意工坊筛选标签，可选 |
| `remote_file_id` | 上传后写入 .mod/descriptor.mod 的创意工坊 ID（**自动生成，别手动删**） |

### descriptor 内容（行顺序无所谓）

```
name="My Mod"
path="mod/My_Mod"            # 根目录 .mod 需要；descriptor.mod 不写 path
tags={
    "Alternative History"
}
picture="thumbnail.png"
supported_version="1.17.*"   # 游戏版本兼容声明（可 "1.17.*" 通配）
version="1.0b"               # mod 自己的版本号
```

**一致性铁律**：本地 `.mod` 与 `descriptor.mod` 的 `name`/`version`/`supported_version` 必须一致，否则启动器与游戏读到的 mod 名/版本不同，表现为"mod 不生效"等诡异问题（静默失败）。

### 更新维护

- **更新即重新上传**：修改后走 launcher 再 Upload，保持 `remote_file_id` 不变（同一 mod 更新而非新 mod）
- **版本号递增**：`version="1.0b"` → `"1.1"`，与 changelog/提交对应（发布绑定审查见 hoi4-review）
- 改文件名/目录会破坏已有订阅与更新链——先想清楚再动
- 发布细节全文见 `references/10-发布与创意工坊.md`

### Python 工具链（HoI4ModdingPythonScripts）

**位置**：`references/HoI4ModdingPythonScripts-master/`（`python2/` 与 `python3/` 两版；Python 2.7 脚本在 Python 3 上不保证可用）。用法说明在各 .py 源码注释中。

| 脚本 | 做什么 | 何时用 |
|---|---|---|
| `hoi4localisationadder.py` | 从事件/国策/想法文件生成空的本地化条目 | 写了大量新 key 后批量补本地化 |
| `hoi4statemapgenerator.py` | 生成州/战略区地图图片（每州独立颜色+ID） | 做地图时可视化州分布 |
| `hoi4fileformatter.py` | 缩进格式化，可读性/一致性 | 整理大文件 |
| `hoi4focusgfxentry.py` | 从国策文件生成 GFX 条目 | 国策图标批量注册 |
| `hoi4ideagfxentry.py` | 为想法生成 idea GFX 条目 | 想法图标批量注册 |
| `hoi4transfertechsegen.py` | 生成 transfer_technology 脚本化效果（一国科技转移给另一国） | 科技转移机制 |
| `DHtoHoi4MinisterConverter` | 把 DH 部长文件转成 HOI4 idea（支持 Unicode） | 移植 Darkest Hour 内容 |
| `USAElectionGenerator.py` | 用 .csv 表格生成美国式选举事件（first-past-the-post） | 选举机制 |
| `hoi4statemanpowermultiplier.py`（仅 python2） | 州人力倍率调整 | 调州人力 |
| `hoi4newspaperheaderadded.py`（仅 python3） | 报纸新闻头添加 | 新闻头批量添加 |

### 脚本使用注意

- **中文乱码**：脚本输出写入文件时用 `utf-8-sig` 编码（对应 yml 需 BOM）
- **不要修改脚本本身**：它们是只读工具；需要定制时复制出来改

## 常见错误表

| 现象 | 原因 | 修复 |
|---|---|---|
| 上传后游戏里 mod 名/版本不对 | 本地 `.mod` 与 `descriptor.mod` 的 name/version 不一致 | 两处统一 |
| 上传列表里找不到自己的 mod | mod 不是用 launcher 创建的 | 用 launcher 新建 mod 后把文件复制进去 |
| thumbnail 不显示/被拒 | 尺寸或格式不对 | 1:1 比例、<1 MB、png |
| 更新后订阅用户收到的是新 mod | 覆盖更新 vs 新上传混淆（remote_file_id 变了） | 保持 remote_file_id，走"更新"而非新建上传 |
| Python 脚本输出中文乱码 | 写入未用 utf-8-sig 编码 | 打开文件时指定编码 |
| 脚本直接报语法错误 | 版本不对（Python 2 脚本在 Python 3 跑） | 用对应 python2/ 或 python3/ 目录的版本 |
| `Invalid supported_version` | 版本号三个点（如 "1.17.3.5"） | 用 `"1.17.*"` 或 `"1.17.3"` |

## Reference Files

| 文件 | 内容 | 何时读 |
|---|---|---|
| `references/10-发布与创意工坊.md` | 发布流程、descriptor 细节、自动化发布与版本管理 | 发布/更新 mod 时 |
| `references/使用 Python 将 Paradox 脚本文件解析.md` | Python 解析 Paradox 脚本文件的原理 | 想自己写脚本时 |
| `references/HoI4ModdingPythonScripts-master/README.md` | 脚本清单与版权（MIT） | 选脚本时先读 |
| `references/HoI4ModdingPythonScripts-master/python2/` | Python 2.7 版脚本 | 对应脚本的运行版本 |
| `references/HoI4ModdingPythonScripts-master/python3/` | Python 3.5 版脚本（含示例 csv） | 对应脚本的运行版本 |

## Cross-Reference

**依赖**：
- **hoi4-mod-workflow** → 编码规则（descriptor.mod 无 BOM）、目录结构
- **hoi4-debugging** → 发布相关报错（descriptor/版本类字典）

**被引用**：
- **hoi4-verification-before-completion** → 发布场景路由（清单 8）
- **hoi4-review** → 发布绑定审查（changelog/补丁说明与提交绑定）
- **hoi4-modding** → 路由表"发布"场景

## Process Interface

任务超出单点查询（要做一个完整发布流程、自动化工具链建设）时，先走 **hoi4-brainstorming** → **hoi4-writing-plans** 流程链，本 skill 只做发布与工具知识支撑。
