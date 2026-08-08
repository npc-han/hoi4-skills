# HOI4 Modding Skills

English | [简体中文](README.md)

A suite of 16 AI-agent skills (Claude Code / Codex / Copilot CLI / Gemini CLI) for Hearts of Iron IV (HOI4) mod development — covering the full workflow: brainstorming and design, implementation, verification and debugging, map-making, and Steam Workshop publishing.

## Quickstart

**Option 1: zip package (recommended)**

1. Download [`dist/hoi4-skills.zip`](dist/hoi4-skills.zip)
2. Unzip to get 16 `hoi4-*` directories
3. Copy all directories into your skills folder:
   - Claude Code: `~/.claude/skills/`
   - Codex / Copilot / Gemini: `~/.agents/skills/`

**Option 2: git clone**

```bash
git clone https://github.com/npc-han/hoi4-skills
```

Then copy the `hoi4-*` directories into your skills folder — zero configuration. In Claude Code, just ask "Add a new focus tree to Germany" or "Help me debug this error" and the matching skill is loaded automatically.

## What's Inside

```
hoi4-modding (router) —— every request is routed here first
├── Knowledge layer (WHAT: syntax / templates / conventions / debugging)
│   ├── hoi4-code-reference      syntax quick-reference (effect / trigger / modifier / scope)
│   ├── hoi4-templates           template skeletons (focus trees / events / decisions / characters / GUI)
│   ├── hoi4-mod-workflow        file dependency chain & encoding conventions (BOM)
│   ├── hoi4-debugging           error dictionary / log reading / launch arguments
│   ├── hoi4-gui-shader          GUI & HLSL shaders
│   ├── hoi4-advanced-systems    advanced systems (special projects / factions / core circles / MIO / AI)
│   ├── hoi4-map-making          maps (states / strategic regions / supply / rendering)
│   ├── hoi4-publishing          Steam Workshop publishing & Python toolchain
│   └── hoi4-version-migration   version migration & mod compatibility
└── Process layer (HOW: design → plan → execute → verify)
    ├── hoi4-brainstorming                   requirement clarification & design (2–3 options)
    ├── hoi4-writing-plans                   implementation plans (dependency-chain ordering / minimal loadable units)
    ├── hoi4-executing-plans                 implement unit by unit, following the plan
    ├── hoi4-verification-before-completion  verification gate before completion
    ├── hoi4-systematic-debugging            systematic debugging (STOP after ≥3 failed attempts)
    └── hoi4-review                          pre-release cross-review (optional gate)
```

## How it works

A typical task runs the full pipeline:

```
Design (brainstorming) → Plan (writing-plans) → Execute (executing-plans) → Verify (verification)
```

- **New feature** ("make a new country / new system") → start with `hoi4-brainstorming`
- **Error / crash / not working** → `hoi4-systematic-debugging` (look up the error via `hoi4-debugging`)
- **Pure lookup** (syntax / templates / map knowledge) → go straight to the knowledge layer, no pipeline
- **Publishing** → verify (`hoi4-verification-before-completion`) first, then publish (`hoi4-publishing`)

Every skill ships with: when to use / when not to use, quick reference, common-mistakes table, and cross-references.

## Philosophy

- **Four checkpoints** run through every process: version anchoring (1.17.x), file dependency chain, encoding/BOM, minimal loadable verification
- The pipeline is **fully self-contained** — no external plugins required
- Source material:
  - Public tutorial collection [Qiuyou Library](https://steamcommunity.com/sharedfiles/filedetails/?id=3445449478) on Steam Workshop
  - [Hearts of Iron IV official wiki](https://hoi4.paradoxwikis.com/) (syntax & mechanics reference)
  - Vanilla game files (`Hearts of Iron IV/game/`) as examples — every template has a matching vanilla counterpart

## Updating

Re-download the zip and overwrite the `hoi4-*` directories in your skills folder (the repo is the source of truth for added/removed files).

## License

[MIT](LICENSE)
