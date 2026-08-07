---
name: hoi4-code-reference
description: Use when writing HOI4 Paradox script code and needing to look up effect syntax, trigger conditions, modifier names, scope types, variable operations, localization formatting, equipment type identifiers, or mod-specific APIs (TNO/TFR). Also use when getting "unknown effect" or "unknown trigger" errors or when an effect silently does nothing
---

# HOI4 Code Reference

## Overview

Complete syntax lookup for HOI4 Paradox script language — effects, triggers, modifiers, scopes, variables, localization, and mod-specific APIs. Reference files provide the full syntax; this skill is the index and quick lookup.

## PDXScript Language Fundamentals

HOI4's scripting language (PDXScript) has four distinct keyword categories. Understanding the difference prevents most syntax errors:

| Category | What It Is | Examples | Where Used |
|---|---|---|---|
| **Effects** | One-time commands that change game state | `add_political_power`, `set_politics`, `start_war` | `complete_effect = { }`, `option = { }`, `on_research_complete = { }` |
| **Triggers** | Boolean conditions that return true/false | `has_government`, `has_war_with`, `check_variable` | `limit = { }`, `available = { }`, `visible = { }`, `allow = { }` |
| **Modifiers** | Persistent numerical adjustments | `political_power_factor`, `army_attack_factor` | Inside ideas, spirits, advisors, ministers, laws |
| **Scopes** | Context-switching keywords | `owner`, `every_country`, `FROM`, `capital_scope` | Anywhere a scope change is needed |

**Execution order**: effects/triggers execute top-to-bottom in order. Modifiers are order-independent. An unrecognized keyword is **silently ignored** — no error in `error.log`, the line simply does nothing.

## Quick Reference

### Most Common Scopes

| Scope | Usage | Target |
|---|---|---|
| `every_country` | Iterate all countries matching limit | Country |
| `random_owned_state` | Random state owned by current scope | State |
| `every_state` | Iterate all states matching limit | State |
| `owner` | Switch to state's owner country | Country |
| `controller` | Switch to state's controller | Country |
| `capital_scope` | Switch to country's capital state | State |
| `random_neighbor_country` | Random neighboring country | Country |
| `every_neighbor_country` | All neighboring countries | Country |
| `every_unit_leader` | All unit leaders of a country | Character |
| `every_character` | All characters matching limit | Character |
| `FROM` | Context reference (caller scope) | Depends on context |
| `ROOT` | Root scope of current chain | Depends on context |
| `PREV` | Previous scope in chain | Depends on context |
| `THIS` | Current scope (self-reference) | Depends on context |

### Scope Chaining

Scopes can be chained with `.` for multi-level references:

| Chain | Meaning |
|---|---|
| `FROM.FROM` | The scope that called the scope that called this scope. In events, `FROM.FROM` is the original sender's `FROM`. |
| `PREV.PREV` | Two scopes back in the enclosing chain |
| `FROM.PREV` | The PREV scope relative to FROM |
| `capital_scope.owner` | The state's capital → the country that owns it |
| `overlord.capital_scope` | The overlord country → its capital state |

**Important**: `FROM` chains shift down one level when events fire. If scope A fires an event with `FROM = B`, inside that event `FROM` is B, `FROM.FROM` is A's FROM (the scope that called A), NOT A itself. Use `save_event_target_as` for reliable cross-event references.

**Caution**: Chaining beyond 3 levels is fragile. Prefer `save_event_target_as` (see below) for complex scope navigation.

### Event Target System

Event targets let you save a scope reference by name and reuse it anywhere, avoiding fragile scope chaining:

| Effect | Persists For | Scope |
|---|---|---|
| `save_event_target_as = my_target` | Until current event chain ends | Any |
| `save_global_event_target_as = my_global` | Entire game session (persists across events, NOT across save/load) | Any |
| `save_temporary_scope_as = my_temp` | Within current effect block | Any |

Using targets:
```
save_event_target_as = target_country           # Save scope
event_target:target_country = { add_political_power = 100 }  # Use it
```

Clear a global target: `clear_global_event_target = my_global`
Clear all: `clear_global_event_targets = yes`

**Variables as scope references**: When a variable stores a tag or state ID, you can scope into it:
```
set_variable = { var_name = POL.id }    # Store Poland's tag ID
var:var_name = {                         # Scope into Poland
    add_political_power = 100
}
```

### Flag System

Flags are boolean markers with scope-specific persistence:

| Flag Type | Set With | Check With | Scope |
|---|---|---|---|
| Country flag | `set_country_flag = my_flag` | `has_country_flag = my_flag` | Country |
| Global flag | `set_global_flag = my_flag` | `has_global_flag = my_flag` | Global |
| State flag | `set_state_flag = my_flag` | `has_state_flag = my_flag` | State |
| Character flag | `set_character_flag = my_flag` | `has_character_flag = my_flag` | Character |
| MIO flag | `set_mio_flag = my_flag` | `has_mio_flag = my_flag` | MIO |

**Extended flag syntax** (1.12+): Flags can carry metadata:
```
set_country_flag = {
    flag = my_timed_flag
    days = 365      # Auto-clears after 365 days
    value = 3       # Numeric value retrievable via check_variable
}
```

Clear a flag: `clr_country_flag = my_flag`

**Flag type must match**: `set_country_flag` requires `has_country_flag` to check — using `has_global_flag` silently returns false.

### Focus Filter Tags

When creating national focuses, use these tags to organize focuses in the in-game UI:

| Tag | Category |
|---|---|
| `FOCUS_FILTER_POLITICAL` | Political / Diplomatic |
| `FOCUS_FILTER_INDUSTRY` | Industrial |
| `FOCUS_FILTER_MANPOWER` | Military / Manpower |
| `FOCUS_FILTER_RESEARCH` | Research / Technology |
| `FOCUS_FILTER_ARMY` | Army |
| `FOCUS_FILTER_NAVY` | Navy |
| `FOCUS_FILTER_AIR` | Air Force |
| `FOCUS_FILTER_ANNEXATION` | Territorial / Annexation |
| `FOCUS_FILTER_STABILITY` | Stability / War Support |

Usage: `FOCUS_FILTER_POLITICAL = yes` inside a `focus = { }` block.

### Technology Syntax Quick Reference

Key fields in `common/technologies/`:

| Field | Purpose | Example |
|---|---|---|
| `research_cost` | Base research cost (1 = ~100 days) | `research_cost = 1` |
| `start_year` | Earliest allowed year (each year ahead = +200% cost) | `start_year = 1936` |
| `path` | Array of prerequisite techs (any one suffices) | `path = { leads_to_tech = basic_infantry }` |
| `enable_equipments` | Equipment unlocked | `enable_equipments = { infantry_equipment_1 }` |
| `enable_subunits` | Unit types unlocked | `enable_subunits = { infantry }` |
| `enable_building` | Buildings unlocked | `enable_building = fort` |
| `on_research_complete` | Effects triggered on completion | `on_research_complete = { add_stability = 0.02 }` |
| `show_effect_as_desc = yes` | Show effects in description instead of tooltip | — |
| `allow` | Conditions required to research | `allow = { has_war = yes }` |
| `allow_branch` | Conditions for branch visibility in UI | `allow_branch = { has_government = fascism }` |
| `categories` | Tech category for UI grouping | `categories = { infantry_tech }` |

### Top 20 Effects (Country Scope)

| Effect | Example | What it does |
|---|---|---|
| `add_political_power` | `add_political_power = 100` | Add immediate PP |
| `add_stability` | `add_stability = 0.05` | Add stability (0.05 = 5%) |
| `add_war_support` | `add_war_support = 0.05` | Add war support |
| `add_manpower` | `add_manpower = 10000` | Add manpower to pool |
| `set_politics` | `set_politics = { ruling_party = communist }` | Change government |
| `add_ideas` | `add_ideas = my_idea` | Grant a national spirit/idea |
| `swap_ideas` | `swap_ideas = { remove = old add = new }` | Replace an idea |
| `remove_ideas` | `remove_ideas = my_idea` | Remove an idea |
| `start_war` | `start_war = { target = TAG }` | Declare war |
| `white_peace` | `white_peace = TAG` | White peace with country |
| `puppet` | `puppet = TAG` | Puppet a country |
| `create_faction` | `create_faction = my_faction` | Create faction |
| `add_to_faction` | `add_to_faction = TAG` | Invite to faction |
| `set_rule` | `set_rule = { desc = TAG_CAN_DECLARE_WAR }` | Set game rule |
| `set_variable` | `set_variable = { var = name value = 100 }` | Set variable |
| `add_to_variable` | `add_to_variable = { var = name value = 50 }` | Add to variable |
| `subtract_from_variable` | `subtract_from_variable = { var = name value = 50 }` | Subtract from variable |
| `multiply_variable` | `multiply_variable = { var_name = 2 }` | Multiply variable |
| `clamp_variable` | `clamp_variable = { var = name min = 0 max = 100 }` | Clamp variable range |
| `load_oob` | `load_oob = my_oob` | Load OOB template |

### Top 20 Triggers (Conditions)

| Trigger | Example | Returns |
|---|---|---|
| `has_government` | `has_government = democratic` | Type of government |
| `has_idea` | `has_idea = my_idea` | Has specific idea |
| `has_war_with` | `has_war_with = TAG` | At war with country |
| `has_political_power` | `has_political_power > 100` | PP threshold |
| `has_manpower` | `has_manpower > 1000` | Manpower threshold |
| `num_of_factories` | `num_of_factories > 50` | Factory count |
| `controls_state` | `controls_state = 123` | Controls state ID |
| `owns_state` | `owns_state = 123` | Owns state ID |
| `is_in_faction` | `is_in_faction = yes` | In any faction |
| `is_in_faction_with` | `is_in_faction_with = TAG` | Same faction as |
| `is_subject_of` | `is_subject_of = TAG` | Is puppet of |
| `has_autonomy_state` | `has_autonomy_state = autonomy_free` | Autonomy level |
| `has_army_experience` | `has_army_experience > 50` | Army XP threshold |
| `has_completed_focus` | `has_completed_focus = TAG_focus_id` | Focus completed |
| `date` | `date > 1939.1.1` | Current date |
| `exists` | `exists = TAG` | Country exists |
| `has_country_leader` | `has_country_leader = { id = 1 }` | Leader traits check |
| `is_puppet` | `is_puppet = yes` | Is a puppet |
| `check_variable` | `check_variable = { var = name value > 50 }` | Variable comparison |
| `has_global_flag` | `has_global_flag = my_flag` | Global flag set |

### Most Common Modifier Codes

| Modifier | What it affects |
|---|---|
| `political_power_factor` | Political power gain multiplier |
| `stability_factor` | Stability change rate |
| `war_support_factor` | War support change rate |
| `research_speed_factor` | Research speed multiplier |
| `production_speed_buildings_factor` | Construction speed |
| `production_speed_arms_factory_factor` | Military factory construction speed |
| `factory_output` | Factory output multiplier |
| `army_core_attack_factor` | Core territory attack bonus |
| `army_core_defence_factor` | Core territory defense bonus |
| `army_attack_factor` | All army attack |
| `army_defence_factor` | All army defense |
| `army_speed_factor` | Army movement speed |
| `army_org_factor` | Army organization |
| `army_recovery_rate_factor` | Army org recovery |
| `navy_strike_force_org_factor` | Navy org |
| `air_attack_factor` | Air attack |
| `air_defence_factor` | Air defense |
| `industrial_capacity_factory` | Factory output per IC |
| `consumer_goods_factor` | Consumer goods need |
| `training_time_factor` | Division training time |
| `political_advisor_cost_factor` | Advisor PP cost |
| `justify_war_goal_time` | War justification time |
| `civil_war_involvement_tension` | Civil war tension threshold |

### Error Message Quick Reference 报错速查

When error.log shows one of these, the cause is almost always:

| Error log shows | Likely cause |
|---|---|
| `Invalid effect 'xxx'` / `Unknown effect-type: xxx` | Typo, version-removed effect, or **TRIGGER used as an effect** (e.g. `can_research`) — check the four keyword categories |
| `Unknown trigger "xxx"` / `Trigger failed to validate ... xxx` | Typo, or referenced object (game rule / idea / focus) not defined |
| `Unknown variable "xxx"` | Variable not set, or name mismatch |
| `invalid database object ... use var:var_name` | Variable used as object ID — read variables with `var:` prefix |
| `invalid scope` | Keyword running in wrong scope — check scope requirements |
| `Malformed block` / `Unexpected token "xxx"` | Unbalanced braces, missing quotes, full-width characters |
| `Could not fire event "xxx"` | Missing `add_namespace`, or event ID doesn't exist |
| Localisation shows raw keys / file errors | Missing BOM, wrong first line (`l_english:`), wrong filename |
| `Could not find texture "gfx/xxx.dds"` | Wrong path, missing file, backslashes instead of forward slashes |
| `Could not find sprite type "GFX_xxx"` | spriteType not registered in `interface/*.gfx`, case mismatch |
| Icons show default `unknown.png` | `GFX_idea_GFX_` double prefix — picture param must not repeat the prefix |
| Focus/decision silently doesn't work | `available` block contains effects instead of triggers |

**Full error dictionary** — every category with root cause and fix, plus the 12-item silent-failure checklist: read `references/常见报错解读.md`.

## Reference Files

When Quick Reference is insufficient, read or grep the full references:

### Core Syntax References (in `references/`)

| File | Contents | When to use |
|---|---|---|
| `常见报错解读.md` | Error dictionary: 12 error categories (unknown keyword/variable/scope/parse/event/localisation/GFX/GUI/OOB/CTD) with error text → cause → fix, error.log entry format, silent-failure checklist, diagnostic decision tree | Reading error.log entries, diagnosing any error or silent failure |
| `提词器1.12.txt` (366KB) | Complete wiki: all effects, triggers, scopes by category. Has table of contents at line 14. | Any effect/trigger/scope syntax lookup. Grep by keyword. |
| `mod常用代码最新修订版2025.8.14.txt` (62KB) | Variables, GUI positioning, font colors (§R etc.), ideology codes, flag operations, event targets | Variable operations, GUI basics, formatting |
| `全学说汇总.txt` | All doctrine mastery nodes and their modifier definitions | Doctrine tree creation |
| `钢4国家精神代码.txt` | All national spirit modifier codes with descriptions | Finding correct modifier name for a concept |
| `钢铁雄心4 指令代码.txt` | Console commands and script commands | Debug/testing commands |
| `本地化wiki.txt` | Localization YAML system reference | `localisation/` file format |
| `装备类型汇总.txt` | Equipment type identifiers | `equipment_bonus` type codes |
| `科技列表（截至抗战DLC）.txt` | Technology IDs by category | Tech tree creation |
| `原版科技种类.txt` | Technology category definitions | Tech file structure |
| `钢4人物trait分类参考.txt` | Character trait classification | Character trait design |
| `海军类别提词器.txt` | Naval-specific effects and modifiers | Naval modding |
| `部分内阁特质提词器.txt` | Cabinet/minister trait codes | Advisor creation |

### Official Paradox Documentation (`references/documentation/`)

| File | Organization |
|---|---|
| `effects_documentation.html` / `.md` | Effects sorted by scope: CHARACTER → COUNTRY → STATE → STRATEGIC_REGION → any |
| `triggers_documentation.html` / `.md` | All trigger conditions with parameters |
| `modifiers_documentation.html` / `.md` | Static modifiers reference |
| `console_commands_documentation.html` / `.md` | All console commands |
| `dynamic_variables_documentation.html` / `.md` | Variable system internals |
| `script_concept_documentation.html` / `.md` | Script concepts explained |
| `loc_formatter_documentation.html` / `.md` | Localization formatting rules |
| `loc_objects_documentation.html` / `.md` | Localization object types |
| `script_documentation.json` | Machine-readable total reference |

### Mod-Specific APIs (`references/`)

| File | Contents |
|---|---|
| `TNO常用代码合集(25.1.5).txt` (35KB) | TNO-specific: economy, GDP, inflation, poverty, social/academic/military development, Cold War GUI |
| `TFR常用代码合集（TFX制作组制）.txt` | TFR-specific: income_growth, personal_value, business_value, tax rates, interest, debt system |

### Modifier Tables (`references/modifiers/`)

| File | Contents |
|---|---|
| `中文modifier修正大全初版（作者：ChiangHeng）.xlsx` | Chinese-localized modifier list |
| `暂定属性修正表.xls` | Provisional modifier table |
| `r_modifiers_l_english.yml` | Vanilla modifier localization strings |

## How to Search

1. **Find an effect name** → grep `references/提词器1.12.txt` by keyword or English name
2. **Check exact effect syntax** → read `references/documentation/effects_documentation.md` — find your scope section, then the effect name
3. **Find a modifier code** → grep `references/钢4国家精神代码.txt` for the concept in Chinese
4. **Variable operations** → read `references/mod常用代码最新修订版2025.8.14.txt` lines 1-40 for all variable effects
5. **Mod-specific APIs** → read `references/TNO常用代码合集(25.1.5).txt` or `TFR常用代码合集（TFX制作组制）.txt`
6. **Official docs** → `references/documentation/` MD files are preferred (easier to grep); HTML files contain the same content with formatting

## Common Mistakes

| Mistake | Error log shows / Symptom | Reality |
|---|---|---|
| `has_idea = yes` | Silent — condition always false | Wrong — `has_idea` checks for a specific idea by ID, not "has any idea" |
| `add_ideas` vs `add_idea` | `Invalid effect 'add_idea'` | Always `add_ideas` (plural), even when adding a single idea |
| `set_variable = { var = name 100 }` | `Malformed block` / parse error | Missing `value =` — correct: `set_variable = { var = name value = 100 }` |
| `add_political_power_factor = 0.1` | `Invalid effect 'add_political_power_factor'` | Modifier `political_power_factor` goes in an idea, not as a direct effect. Use `add_political_power = N` for immediate PP. |
| Using country-scope modifiers in state scope | `Unknown modifier` / silent no-effect | State modifiers have different names — check `references/documentation/modifiers_documentation.md` |
| Variable reference without `var:` prefix | `Unknown variable` / `use var:var_name` | When reading a variable, use `var:my_var`; when setting, use `var = my_var` (no colon) |
| `every_country = { limit = { ... } }` | `Malformed block` | Correct syntax is `every_country = { limit = { ... } ... }` — `limit` goes INSIDE the scope block |
| Effect used in `available = { }` | `Invalid effect 'xxx'` at that line | `available` only takes triggers, not effects. Effects go in `complete_effect`. Use `if = { limit = { ... } ... }` for conditional effects. |
| `set_country_flag` checked with `has_global_flag` | Silent — check always false | Flag type must match — `set_country_flag` → `has_country_flag` |
| Chaining `FROM.FROM.FROM` across event boundaries | Silent — wrong target scoped | `FROM.FROM` inside an event is NOT necessarily the original caller. Use `save_global_event_target_as` for cross-event references. |
| Unrecognized keyword in effect block | Silent — ignored, no error | Unrecognized keywords are silently ignored. Always verify effect names against **hoi4-code-reference** reference files. |
| `add_namespace` missing in events | Silent — events never fire | Events silently won't fire. Always declare `add_namespace = my_mod` at the top of every event file. |
| `.yml` missing BOM / wrong first line | Localisation errors in error.log / raw keys in game | `.yml` needs UTF-8 with BOM and `l_english:` (or `l_simp_chinese:`) on line 1 — see **hoi4-mod-workflow** |
| Backslashes in paths | Texture/sprite not found or mod not loading | Use forward slashes `/` in all paths — backslashes are escape characters |

## Cross-Reference

- **hoi4-templates**: Complete file skeletons for every mod file type — use after looking up syntax here
- **hoi4-mod-workflow**: The correct dependency order for creating interconnected mod files
- **hoi4-version-migration**: Check if an effect/modifier was deprecated or renamed in your target version
- **hoi4-gui-shader**: Advanced GUI scripting and shader code — references variable syntax covered here
