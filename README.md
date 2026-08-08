# HOI4 Modding Skills（钢4 MOD 制作技能体系）

16 个 AI Agent 技能（Claude Code / Codex / Copilot CLI / Gemini CLI），覆盖钢铁雄心 4（Hearts of Iron IV）MOD 制作全流程——从构思设计到代码实现、验证调试、地图与创意工坊发布。

## 快速开始（Quickstart）

**方式一：zip 安装包（推荐）**

1. 下载 [`dist/hoi4-skills.zip`](dist/hoi4-skills.zip)
2. 解压，得到 16 个 `hoi4-*` 目录
3. 复制全部目录到技能目录：
   - Claude Code：`~/.claude/skills/`
   - Codex / Copilot / Gemini：`~/.agents/skills/`

**方式二：git clone**

```bash
git clone https://github.com/npc-han/hoi4-skills
```

然后同上，把 `hoi4-*` 目录复制到技能目录，零配置即用。对 Claude Code 说"给德国加一个国策""报错了帮我查"等即可自动调用对应技能。

## 包含什么（What's Inside）

```
hoi4-modding（路由中枢）—— 所有请求先路由
├── 知识层（WHAT：语法/模板/规范/排查）
│   ├── hoi4-code-reference      语法速查（effect/trigger/modifier/scope）
│   ├── hoi4-templates           模板骨架（国策/事件/决议/人物/GUI）
│   ├── hoi4-mod-workflow        文件依赖链与编码规范（BOM）
│   ├── hoi4-debugging           报错字典/日志解读/启动参数
│   ├── hoi4-gui-shader          GUI 与 HLSL 着色器
│   ├── hoi4-advanced-systems    进阶系统（特殊项目/阵营/核心圈/MIO/AI）
│   ├── hoi4-map-making          地图（省份/战略区/补给/渲染）
│   ├── hoi4-publishing          创意工坊发布与 Python 工具链
│   └── hoi4-version-migration   版本迁移与 mod 兼容
└── 流程层（HOW：设计→计划→执行→验证）
    ├── hoi4-brainstorming             需求澄清与设计（2-3 方案）
    ├── hoi4-writing-plans             实施计划（依赖链排序/最小可加载单元）
    ├── hoi4-executing-plans           按计划逐单元实现
    ├── hoi4-verification-before-completion  完成前验证闸门
    ├── hoi4-systematic-debugging      系统化调试（失败≥3 次 STOP）
    └── hoi4-review                    发布前交叉审查（可选门）
```

## 如何使用（How it works）

一次典型任务走完整流程链：

```
设计（brainstorming）→ 计划（writing-plans）→ 执行（executing-plans）→ 验证（verification）
```

- **新功能**："做个新国家/新系统" → 从 `hoi4-brainstorming` 开始
- **报错/崩溃/没生效** → `hoi4-systematic-debugging`（报错查证用 `hoi4-debugging`）
- **纯查询**（语法/模板/地图知识）→ 直接进知识层，不启动流程链
- **发布** → 先验证（`hoi4-verification-before-completion`）再发布（`hoi4-publishing`）

每个技能自带：何时用/何时不用、快速参考、常见错误表、交叉引用。

## 设计理念（Philosophy）

- **四大检查点**贯穿所有流程：版本锚定（1.17.x）、文件依赖链、编码/BOM、最小可加载验证
- 流程链**完全自包含**，不依赖任何外部插件
- 知识素材来自 [秋起图书馆（霜泽图书馆）](https://steamcommunity.com/sharedfiles/filedetails/?id=3445449478) 公开教程

## 更新（Updating）

重新下载 zip 并覆盖 `~/.claude/skills/` 下的 `hoi4-*` 目录即可（文件增删以仓库为准）。

## License

[MIT](LICENSE)
